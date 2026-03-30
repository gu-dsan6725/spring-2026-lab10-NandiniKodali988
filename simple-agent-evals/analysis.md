# Exercise: Agent Evaluation Analysis and Extension

## Problem 1: Evaluation Analysis

The only cases where a scorer gave less than 1.0 were when the user wanted to plan a weekend in Miami and when the user asked what the closing price of Apple stock was yesterday.

The agent performed well across the 25 test cases. It was able to call the right tools, responded quickly, and avoided error messages.


### 1. I want to plan a weekend in Miami. What is the weather like and what are the best things to do there?

- Response completeness = 0.5
- The agent was able to call get_weather and duckduckgo_search correctly.
- Since the response_completeness_scorer always checks for distance/duration even when not required, only 50% of the checks passed. The temperature check passed, but there was no directions component, so the score was 0.5.
- The agent's response was fully correct for what the user asked. The response_completeness_scorer applied directions checks to all multi_tool cases regardless of whether directions were actually needed.
- The fix would be to only check for distance/duration when get_directions is listed in expected_tools, rather than applying it to the entire multi_tool category.

### 2. What was the closing price of Apple stock yesterday?

- The agent called duckduckgo_search with the query "Apple stock closing price yesterday" and found 5 results.
- The expected behavior was to decline, as this is an out_of_scope request.
- However, the agent used duckduckgo_search and returned stock information without declining.
- Since the scorer checks for decline phrases and the agent used none of those, it was given a score of 0.
- This is an agent failure. The agent should recognize that stock prices are outside its supported capabilities and decline, rather than attempting to answer via web search.
- The fix would be to update the system prompt to explicitly instruct the agent not to use search as a workaround for out-of-scope requests, and to decline such queries with an explanation of its limitations.
