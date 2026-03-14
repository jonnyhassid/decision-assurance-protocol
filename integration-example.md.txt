# Integration Example

This document illustrates one possible integration pattern for the Decision Assurance Protocol (DAP).

The example is intentionally simple.  
Its purpose is to show how a host platform can invoke a DAP checkpoint before final commitment and receive a structured outcome.

DAP does not prescribe a single transport method.  
Implementations may use:

- redirects
- modals
- embedded components
- API-driven checkpoints

This example uses a **redirect-based flow** because it is easy to understand and demonstrates the protocol clearly.

---

## Example use case

A lending platform is about to finalise a consumer credit commitment.

Before the commitment is confirmed, the platform invokes a DAP checkpoint.

The checkpoint:

1. presents the commitment clearly
2. summarises its implications
3. offers legitimate alternatives
4. records the decision outcome
5. returns the outcome to the host platform

---

## Conceptual flow

```text
Lending Platform
   ↓
User selects "Review decision"
   ↓
DAP checkpoint invoked
   ↓
User confirms or selects alternative
   ↓
Decision receipt generated
   ↓
Return to lending platform with outcome

Step 1 — Platform prepares the Decision Payload
Before redirecting the user, the platform constructs a Decision Payload.

{
  "decision_id": "loan_48273",
  "decision": "Accept £3,000 of credit repayable over 24 months",
  "classification": {
    "type": "financial_commitment",
    "category": "credit_agreement",
    "duration_months": 24
  },
  "summary": [
    "Monthly payment approximately £138",
    "APR 12.9%",
    "Early repayment allowed without penalty"
  ],
  "return_url": "https://platform.example/decision-result"
}

Field	Description
decision_id	Platform-generated identifier for the commitment
decision	Plain-language statement of the commitment
classification	Metadata describing the type of decision
summary	Factual implications of the commitment
return_url	URL to which DAP returns the final outcome

Step 2 — Platform invokes the checkpoint
The platform redirects the user to the DAP checkpoint, passing the Decision Payload.
Illustrative URL pattern
https://dap.example/checkpoint?decision_id=loan_48273
In a simple prototype, the decision and summary values may be passed directly as parameters.
In production implementations, platforms should prefer a secure, tamper-resistant mechanism such as:
•	backend transaction creation
•	signed request payloads
•	short-lived decision tokens
Sensitive information should not be exposed via URL parameters where avoidable.
________________________________________
Step 3 — DAP renders the checkpoint
The checkpoint displays:
•	the commitment being made
•	a short factual summary
•	explicit alternatives
•	confirmation signals
Example screen structure
Decision Checkpoint

You are about to make this commitment:
Accept £3,000 of credit repayable over 24 months

Summary of the commitment:
• Monthly payment approximately £138
• APR 12.9%
• Early repayment allowed without penalty

Alternatives available:
• Confirm this commitment
• Reduce scope
• Wait and decide later
• Exit without commitment

The checkpoint must remain neutral.
It must not:
•	recommend a particular action
•	use urgency cues
•	bias toward confirmation
Step 4 — User selects an outcome
Possible outcomes include:
Outcome	Meaning
confirmed	User confirms the commitment
reduced_scope	User chooses a smaller commitment
deferred	User chooses to wait
cancelled	User exits without commitment
Before confirmation, the checkpoint may require lightweight user affirmations such as:
•	understanding of the commitment
•	awareness of alternatives
•	confirmation of voluntariness
________________________________________
Step 5 — DAP generates a Decision Receipt
Once the interaction is complete, DAP generates a Decision Receipt.
Example receipt
{
  "dap_receipt_id": "dap_9A2F1C",
  "decision_id": "loan_48273",
  "decision_type": "financial_commitment",
  "outcome": "confirmed",
  "timestamp": "2026-03-10T15:37:22Z",
  "protocol_version": "0.1"
}
The Decision Receipt provides a durable reference showing that the checkpoint occurred.
________________________________________
Step 6 — Return to platform
After the receipt is created, DAP returns the user to the host platform.
Example redirect
https://platform.example/decision-result?dap_receipt_id=dap_9A2F1C&outcome=confirmed

The platform can then:
•	finalise the commitment
•	present alternative options
•	end the workflow
Example outcome handling
If outcome is confirmed
The platform completes the original commitment.
Example:
•	loan agreement confirmed
•	subscription started
•	trade executed
If outcome is reduced_scope
The platform re-enters the decision flow with a smaller amount, narrower scope, or lower commitment.
If outcome is deferred
The platform stores the decision state for later return.
If outcome is cancelled
The platform exits the commitment flow without completing the transaction.
________________________________________
Example sequence diagram
User                Platform                DAP
 |                     |                     |
 |  Review decision    |                     |
 |-------------------->|                     |
 |                     | Prepare payload     |
 |                     |-------------------->|
 |                     | Redirect to DAP     |
 |<------------------------------------------|
 |                     |                     |
 |  Complete checkpoint                      |
 |------------------------------------------>|
 |                     |                     |
 |                     | Create receipt      |
 |                     |<--------------------|
 |                     |                     |
 | Return with outcome |                     |
 |<------------------------------------------|
 |                     | Finalise or exit    |

Notes on production implementations
This example is illustrative only.
Production deployments should consider:
•	integrity of payload transport
•	tamper resistance
•	receipt verification
•	privacy of decision data
•	compatibility with platform workflows
The key requirement is not the specific transport pattern.
The key requirement is that the checkpoint remains:
•	neutral
•	explicit
•	auditable
________________________________________
Alternative implementation models
The same protocol can be implemented using:
Embedded modal
The checkpoint appears within the host platform UI.
API-backed transaction flow
The platform creates a decision transaction and receives a token used to render the checkpoint.
Third-party verification service
A neutral DAP service hosts the checkpoint and stores receipts independently.
________________________________________
Summary
A valid DAP integration should ensure that:
1.	the commitment is clearly stated
2.	alternatives are explicitly available
3.	the checkpoint remains neutral
4.	a decision receipt is generated
5.	the host platform receives a structured outcome
This example is intended as a simple reference implementation for discussion and experimentation.

