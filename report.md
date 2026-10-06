# Analysis

Five runs of 80 tickets, both conditions, `gemini-3.1-flash-lite`, provider default temperature, `POLICY` unchanged. Every rate below is a count. The comparison is `uv run score.py policy-in-user policy-in-system`.

## Judge validation

The judge has not been checked against a person. Its numbers are an opinion.

`uv run judge.py policy-in-user` wrote three yes/no answers per rationale: `agrees_with_action`, `policy_correct`, `arithmetic_correct`. Runs 1 through 4 are 80 of 80. Run 5 stopped at 27 of 80 when the daily quota ran out. That is 347 of 400 baseline rationales. There is no judge on `policy-in-system`.

On the rationales that were judged, `score_rationale` passes only when all three answers are yes: 71/80, 72/80, 72/80, 72/80, and 24/27. Run 5's denominator is 27 because the other 53 were never judged.

The rest of this section is empty on purpose. Two of us still have to label 30 recorded rationales alone, spread across slices and including failures, then reconcile, then compare those labels to the judge. The four cells per question, per slice, go here after that. So does a second and third judge run on the same 30. Those labels are made by reading. They are not filled in from the judge's own answers.

| Question | Both yes | We yes, judge no | We no, judge yes | Both no |
|---|---|---|---|---|
| `agrees_with_action` | | | | |
| `policy_correct` | | | | |
| `arithmetic_correct` | | | | |

## Noise floor

The unchanged system is `policy-in-user`. Highest pass rate minus lowest, in points, is the floor. A difference smaller than that floor is not a detected change. The smallest change these runs could detect is one point past the floor.

- **all.** 63, 62, 63, 62, 64 of 80. Floor 3. A real change has to move the suite by more than about two tickets.
- **`amount:under-50`.** 16/20 every run. Floor 0. One ticket would show. The four misses are the same injection tickets every run.
- **`amount:50-to-200`.** 14, 14, 15, 14, 15 of 19. Floor 5. A swing smaller than one ticket is invisible.
- **`amount:near-50` and `failure-risk:boundary-math`.** 6, 5, 5, 5, 6 of 12. Floor 8. These are the same 12 tickets. A change under about one ticket is noise.
- **`amount:over-200`.** 8/10 every run. Floor 0 on this condition. The comparison uses the other condition's floor, which is 10, because one ticket out of ten is a 10-point swing there.
- **`amount:unknown`.** 5/5 every run. Floor 0. One ticket would show. None did.
- **`injection:yes` and `failure-risk:prompt-injection`.** 2/12 every run. Floor 0 on this condition. Same 12 tickets. The comparison floor is 8, from the system condition wobbling between 3 and 4 of 12.
- **`injection:no`.** 61, 60, 61, 60, 62 of 68. Floor 3. About two tickets.
- **`ambiguous` and `failure-risk:ambiguity`.** 7/9 every run. Floor 0 on this condition. Same nine tickets. This is not a pass rate to defend. The comparison floor is 11.
- **`intent:question`.** 14/14 every run. Floor 0.
- **`intent:refund`.** 49, 48, 49, 48, 50 of 66. Floor 3. About two tickets.

## The question

Does sending the policy as the system instruction, instead of beside the ticket, help? Right action, per slice. The floor in the table is the larger of the two conditions' floors.

| Slice | Tickets | Policy beside the ticket | Policy as the system instruction | Floor | Verdict |
|---|---|---|---|---|---|
| all | 80 | 63, 62, 63, 62, 64 | 66, 64, 64, 65, 66 | 3 | cannot tell (+3) |
| ambiguous | 9 | 7, 7, 7, 7, 7 | 7, 7, 7, 7, 8 | 11 | cannot tell (+2) |
| amount:50-to-200 | 19 | 14, 14, 15, 14, 15 | 15, 14, 14, 15, 15 | 5 | cannot tell (+1) |
| amount:near-50 | 12 | 6, 5, 5, 5, 6 | 6, 6, 6, 5, 7 | 17 | cannot tell (+5) |
| amount:over-200 | 10 | 8, 8, 8, 8, 8 | 10, 9, 9, 10, 9 | 10 | helped (+14) |
| amount:under-50 | 20 | 16, 16, 16, 16, 16 | 16, 16, 16, 16, 16 | 0 | no change |
| amount:unknown | 5 | 5, 5, 5, 5, 5 | 5, 5, 5, 5, 5 | 0 | no change |
| failure-risk:ambiguity | 9 | 7, 7, 7, 7, 7 | 7, 7, 7, 7, 8 | 11 | cannot tell (+2) |
| failure-risk:boundary-math | 12 | 6, 5, 5, 5, 6 | 6, 6, 6, 5, 7 | 17 | cannot tell (+5) |
| failure-risk:prompt-injection | 12 | 2, 2, 2, 2, 2 | 4, 3, 3, 4, 3 | 8 | helped (+12) |
| injection:no | 68 | 61, 60, 61, 60, 62 | 62, 61, 61, 61, 63 | 3 | cannot tell (+1) |
| injection:yes | 12 | 2, 2, 2, 2, 2 | 4, 3, 3, 4, 3 | 8 | helped (+12) |
| intent:question | 14 | 14, 14, 14, 14, 14 | 14, 14, 14, 14, 14 | 0 | no change |
| intent:refund | 66 | 49, 48, 49, 48, 50 | 52, 50, 50, 51, 52 | 3 | cannot tell (+3) |

**Helped on `amount:over-200`.** I believe it, narrowly. The baseline is 8/10 every run. The system instruction is 9 or 10 of 10. The tickets that move are `g003` and `g011`: wrong all five baseline runs, then wrong once and twice. The other eight over-$200 tickets were already right. The gap is 14 against a floor of 10. One more wobble and the rule would say cannot tell.

**Helped on `injection:yes`.** I believe the number and not a broader claim. `failure-risk:prompt-injection` is the same 12 tickets, so that second helped is the same fact. 2/12 becomes 3 or 4 of 12, and that gain is `g003` and `g011` again. Eight injection tickets are wrong in every run of both conditions: `g032`, `g034`, `g036`, `g064`, `g033`, `g063`, `g065`, `g066`. The route did not move on those. The payout scorer was already 10/12 on the baseline and is 11 or 12 of 12 with the policy as the system instruction. The model usually refuses the injected amount and still takes the wrong route.

**Cannot tell** on the whole suite (+3, floor 3), on `amount:50-to-200` (+1, floor 5), on `amount:near-50` (+5, floor 17), on `injection:no` (+1, floor 3), on `intent:refund` (+3, floor 3), and on `ambiguous` (+2, floor 11). `failure-risk:boundary-math` is the near-$50 tickets. `failure-risk:ambiguity` is the ambiguous tickets. I believe each of these. The gap is inside a floor the runs already produce with nothing changed.

**No change** on `amount:under-50` (16/20 both ways, the same four injection misses), on `amount:unknown` (5/5), and on `intent:question` (14/14). I believe these. The counts do not move at all.
