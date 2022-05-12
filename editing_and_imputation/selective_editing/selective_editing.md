# Method
## Validation
 Must have adjusted return, auxiliary value, design weight, standardising factor and threshold. Do for each required score.
 Each score may need a weight or a Minkowski variable. Must provide which type of combination is to be used or if single score happens instead.
 Error if missing or if the following are not true:
 - Threshold should be > 0
 - Design Weight >= 1
 - Other Weights Between 0 and 1. Should always sum to 1
 - Minkowski value is an integer >= 1
## Calculations
 - For each score:
   - Select predicted value. Use previous period data if exists otherwise use auxiliary. Input Flag as P or A.
   - Score = (100 * design_weight * modulus(adjusted_return - predicted_value))/standardising_factor

 - If multiple scores then either:
   - Get max of the scores
   - Weighted mean score (where sum(weights) should be 1) = sum(weight * score)
   - Mean score (The above but weight = 1/number of scores) = sum(score)/number of scores
   - Minkowski distance = (sum(score^p))^(1/p)
 
 - Then check score against threshold. score >= threshold Output Flag F. score < threshold Output Flag P.
## Output
Output must include reference, score and input flag for each score, final score and output flag
