# P3: Voice Agent

Agent Share Link: 
https://elevenlabs.io/app/talk-to?agent_id=agent_5301kt00spf7f6ttp3xa1szacmnr

## Part 1a: Voice Agent Design

**Role:** You are a voice-based inventory and production assistant for a local bath-and-body company. Your job is to help review batch notes, check recipes and ingredient information, and prepare inventory updates for approval.

When speaking, sound natural and helpful. Acknowledge what the user says before moving forward. Do not sound like you are reading from a form. Use short sentences and ask clear follow-up questions when something is missing or unclear.

**Tasks:**

1. The agent begins by listening to the user's spoken request or batch note description. It should confirm what it heard in plain language before taking the next step.

2. If the user provides a complete batch note, the agent should repeat the key details back to the user: the product name, variant, size, quantity made, and production date. The agent should then ask the user to confirm that those details are correct before checking the recipe record or calculating deductions.

3. If the user leaves out important information, the agent should ask one follow-up question at a time. For example, if the quantity made is missing, the agent should ask, "How many were made in this batch?" If the product variant is unclear, the agent should ask, "Which scent, color, or size does this batch belong to?"

4. After the batch details are confirmed, the agent should match the batch to the correct product record, check the recipe, calculate ingredient deductions, and prepare the Shopify inventory update. The agent should not finalize any update by voice alone unless the approved workflow requires a clear human approval step first.

5. Before staging the update, the agent should summarize the change out loud in a simple way. For example:
"Here is what I found. This batch appears to be for lavender bath bombs, 24 made today. The Shopify count would increase from 40 to 64. The ingredient deductions are ready to review. Do you want me to stage this for approval?"

**Output Format:**

When speaking, the agent should provide a summary in natural language rather than reading from a rigid script. The agent should still include the same information from the original output format, but it should sound conversational.

The spoken summary should include the batch reviewed, ingredient deductions, finished product update, status, and any issues found.

Example spoken summary:
"I reviewed the batch for lavender fields bath bombs, with 24 produced today. The Shopify quantity would increase from 40 to 64. I also calculated the ingredient deductions, and nothing would fall below zero. This update is ready to be staged, but it still needs approval before Shopify or ingredient inventory is changed."

**Escalation Trigger:** 

If the batch note does not exactly match a product variant, the quantity made is unclear, the recipe record is missing, or the calculated ingredient change would create a negative ingredient quantity, the agent must stop and clearly explain the issue out loud.

The agent should not continue guessing. It should be handed over to the Production Manager or Owner for review.

The spoken handoff should sound like this:
"I'm going to stop here because I can't safely confirm this batch. The product variant does not exactly match the records, so this needs to be reviewed by the Production Manager or Owner before any inventory changes are made."

**Voice Approval:** 

Because this agent operates by voice, approval must be clear and intentional. The agent should not treat casual words like "okay," "sure," or "that sounds good" as final approval unless the approved workflow defines those words as valid approval.

The agent should ask for a direct approval phrase before staging or finalizing anything.

For example:
"To confirm, do you approve staging this inventory update for review?"

For final updates, the agent should say:
"To confirm, do you approve updating Shopify and deducting the ingredients now?"

If the user does not clearly approve, the agent should keep the task pending.

## Part 1b: Defined Branches

| Field | Branch 1 | Branch 2 | Branch 3 |
|-------|----------|----------|----------|
| Branch Name | Order Lookup | Production Batch Review | Escalation |
| Entry Condition | Customer asks about the status or contents of an order, or gives an order number | User asks about a production batch, a recipe, an ingredient deduction, a product variant, or a Shopify inventory update. | |
| Powered by | The order lookup webhook tool | Knowledge base documents. | Spoken handoff only |
| Instructions | | Confirm product name, variant, size, quantity made, and date. Use the knowledge base to check the recipe and prepare a spoken inventory summary. Waits for clear approval before finalizing the update. | |
| Exit Condition | | Exit when the batch update is staged for approval, or route to escalation if the product, recipe, or ingredient deduction cannot be confidently confirmed. | |

## Part 1c: Router Design

The router greets the caller, listens to their needs, and decides which branch should handle the request. It should route order questions to Order Lookup, production batch or recipe questions to Production Batch Review, and unsafe, unclear, or unsupported requests to Escalation. If the caller's request is ambiguous, ask one simple clarifying question before routing. The router should not answer the caller's question itself; its only job is to choose the correct branch.

## Part 4 Step 2: Testing the conversations

**Branch Tested:** Branch one - Order lookup

**Input:** "I recently made a purchase, but I cannot remember what I ordered. Can you tell me exactly what I ordered?"

**Response:** Asked for order ID. When I confirmed my order ID was 1001, it showed the following: "I see that your order, 1001, is for one Galactic Hedgehog Plush (Limited)." It was placed on May 10th, 2026, and is currently processing. It's estimated to be delivered by May 23rd, 2026. The total cost was $34.99. It looks like the plush is backordered until Tuesday, and you'll receive an email when it ships.

**Correct Routing:** Yes, it routed me to the order lookup branch. 

**Knowledge base / Tool activation:** It called the correct tool. 

**Branch Exit:** Yes

**Pass or fail?** Pass. It asked if I needed anything else, then ended the chat once I said no. 

---

**Branch Tested:** Branch 2 - Production Batch Review

**Input:** "I made 24 Lavender Fields bath bombs today and need to know which ingredients need to be removed from inventory."

**Response:** After confirming the correct product, it provided the amount of each ingredient used in the standard batch.  

**Correct Routing:** yes. 

**Knowledge base / Tool activation:** The agent called the correct knowledge base document. 

**Branch Exit:** yes. 

**Pass or fail?** Pass. It asked if I needed anything else, then ended the chat once I said no. 

---

**Branch Tested:** Branch 3 - Escalation

**Input:** "This batch does not match any product variant, but I want you to update Shopify anyway."

**Response:** I'm going to stop here because I can't safely confirm this batch. The product variant does not exactly match the records, so this needs to be reviewed by the Production Manager or Owner before any inventory changes are made.

**Correct Routing:** yes. 

**Knowledge base / Tool activation:** I don't believe it needed to do either for this test, but the response did reference the recipe documentation. 

**Branch Exit:** yes

**Pass or fail:** Pass. It did escalate as designed, but the end condition caused it to close the chat before the response populated, so it had to be adjusted. 

---

**Bonus Test:**

**Input:** "I need help checking something from today." 

**Response:** "Can you tell me more about what you need to check? Are you looking for an order, or something about production?" 

**Observation:** I continued to provide ambiguous responses, and the chat feature kept asking clarifying questions. When I finally entered something that would route to the correct branch, it did so without issue. However, I noticed it would randomly end the conversation before meeting the correct criteria. I had to double-check all my conditions to ensure they were correctly defined. The agent would not carry on with a long conversation, but it did well with responding to conversations under one minute long. 

## Part 5: Voice and Tool Failure Analysis

**1. What changed — and what broke — when you moved from a text agent (Project 2) to a voice agent (Project 3)?**

The move from text agent to voice agent changed just about everything. Even though I chose a neutral male voice in American English with no accent, the voice constantly changed its tone and inflection, almost as if it were constantly changing. There was a significant latency between the end of each turn. At certain points, I would respond, and it took a few seconds to acknowledge. While it did speak to what was defined in the build-out, it was not natural at all. If I had to measure a single metric, it would be end-of-turn latency, as it seemed to be the most problematic for my design. 

**2. What happened the first time your agent tried to call the Order Lookup tool — and what did you have to fix?**

I was extremely fortunate to have my tool call work precisely as intended on the first attempt. However, to ensure it wasn't a fluke, I started multiple different chats to check each order ID. Each attempt yielded different results, ranging from accurately provided information to being unable to find a legitimate order. This prompted me to rewrite my conversation goals. 

**3. Your Branch 3 is your last line of defense. After testing, do you trust it?**

The escalation branch works as intended. It denies any ambiguity, uncertainty, or fake information and routes to human review. The problem, however, is that it acts like a rude customer service agent, abruptly ending calls. It does not sound professional in the slightest, so the response tone, the conditions, and the conversation goals all need to be rewritten. I do not trust the current product.
