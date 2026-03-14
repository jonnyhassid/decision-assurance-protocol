# Decision Assurance Protocol (DAP)
Protocol Specification — Draft v0.1

---

## 1. Overview

Decision Assurance Protocol (DAP) defines a lightweight standard for recording the context of consequential digital decisions.

DAP introduces a neutral checkpoint immediately before a user confirms a significant commitment within a digital system.

The checkpoint performs four core functions:

1. Clearly names the commitment being made.
2. Explicitly recognises legitimate alternatives.
3. maintains strict neutrality (no advice, optimisation, or persuasion).
4. Creates a structured, auditable record of the decision context.

DAP is designed as a thin verification layer between **intent and commitment**.

The protocol does not alter the underlying transaction, contract, or platform workflow.  
It records the context in which a commitment was confirmed.

---

## 2. Design goals

DAP is designed to satisfy the following properties.

### 2.1 Thinness

The protocol should introduce minimal additional logic into existing platform workflows.

DAP does not:

- provide recommendations
- perform suitability analysis
- optimise user behaviour
- replace regulatory compliance processes

### 2.2 Neutrality

DAP must not influence the user's decision.

The checkpoint must avoid:

- persuasive framing
- behavioural nudges
- default bias toward commitment

### 2.3 Legibility

The commitment being made must be described clearly in plain language.

### 2.4 Auditability

The decision context and outcome must be recorded in a form that can be verified later.

### 2.5 Platform independence

DAP should operate as a reusable protocol across multiple sectors including:

- financial services
- subscription commerce
- digital permissions
- contractual agreements

---

## 3. Core protocol invariants

Every valid DAP checkpoint must implement the following invariants.

### 3.1 Decision naming

The commitment being confirmed must be stated clearly.

Example: Accept £3,000 of credit repayable over 24 months


### 3.2 Commitment summary

A short list of factual implications of the commitment must be presented.

Example:

- Monthly payment amount
- Duration of commitment
- Interest rate
- Early repayment conditions

### 3.3 Explicit alternatives

At least two non-commitment alternatives must be presented.

Examples include:

- Reduce scope
- Delay decision
- Exit without commitment

These alternatives must be presented as legitimate outcomes.

### 3.4 Confirmation signals

The checkpoint must capture user confirmations indicating:

- understanding of the commitment
- awareness of alternatives
- voluntary decision

### 3.5 Decision outcome

The checkpoint must produce one of the following outcomes:

| Outcome | Description |
|-------|-------------|
| Confirmed | The user confirms the commitment |
| Reduced scope | The user chooses a smaller commitment |
| Deferred | The user delays the decision |
| Cancelled | The user exits the commitment |

---

## 4. Protocol flow

A typical implementation follows this sequence.
Platform
↓
User initiates commitment
↓
DAP Decision Checkpoint
↓
User selects outcome
↓
Decision receipt generated
↓
Return to platform with outcome


DAP may be implemented through:

- redirect flows
- embedded components
- modal interfaces
- API-driven checkpoints

The specific implementation is not prescribed by this specification.

---
## 5. Protocol message format
DAP implementations exchange structured decision data between platforms and the decision checkpoint.
The protocol defines two core message types:
1.	Decision Payload
2.	Decision Receipt
The specific transport mechanism (redirect, API call, embedded component) is not prescribed.
________________________________________
5.1 Decision payload
The platform initiating the checkpoint provides a Decision Payload describing the commitment being verified.
Example payload
{
  "decision_id": "merchant-generated-id",
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
decision_id	Platform identifier for the decision
decision	Plain-language description of commitment
classification	Decision classification metadata
summary	Bullet points summarising implications
return_url	URL to return decision outcome

Optional fields	
Field	Description
financial_amount	Monetary value
currency	Currency code
merchant_context	Platform-specific metadata

5.2 Decision outcomes
After the checkpoint interaction completes, the protocol returns a Decision Outcome.
Possible values include:
Outcome	Meaning
confirmed	User confirms commitment
reduced_scope	User chooses smaller commitment
deferred	User delays decision
cancelled	User exits commitment
________________________________________
5.3 Decision receipt
A successful checkpoint generates a Decision Receipt.
Example receipt
{
  "dap_receipt_id": "dap_9A2F1C",
  "decision_id": "merchant-generated-id",
  "decision_type": "financial_commitment",
  "outcome": "confirmed",
  "timestamp": "2026-03-10T15:37:22Z",
  "protocol_version": "0.1"
}
Required fields
Field	Description
dap_receipt_id	Unique receipt identifier
decision_id	Original decision identifier
outcome	Final checkpoint result
timestamp	Completion time
protocol_version	DAP version used

Return flow
After the checkpoint completes, the system returns to the platform with the receipt reference.
Example redirect: https://platform.example/decision-result?dap_receipt_id=dap_9A2F1C&outcome=confirmed
The platform may then:
•	finalise the commitment
•	present alternative options
•	cancel the transaction

## 6. Decision classification model

DAP includes a lightweight classification layer to allow decisions to be analysed and audited consistently.

Each decision record should include a classification consisting of:

| Field | Description |
|------|-------------|
| Decision type | High-level category of commitment |
| Category | Domain-specific classification |
| Duration | Estimated duration of the commitment |
| Financial impact | Optional monetary dimension |

Example classification:
Decision type: Financial commitment
Category: Credit agreement
Duration: 24 months


Classification models may evolve over time.

---

## 7. Decision receipt

When a decision checkpoint completes, a **Decision Receipt** should be generated.

The receipt records the decision context and outcome.

### Example receipt
Decision ID: DAP-A9C8-2026
Decision type: Credit agreement
Outcome: Commitment confirmed
Recorded: 2026-03-10T15:37:22Z
Protocol version: 0.1


### Minimum receipt fields

| Field | Description |
|------|-------------|
| Decision ID | Unique identifier |
| Decision type | Classification |
| Outcome | Result of checkpoint |
| Timestamp | Time of completion |
| Protocol version | DAP version used |

Receipts may optionally include additional metadata.

---

## 8. Decision logs

Platforms implementing DAP may maintain decision logs.

Logs allow decision checkpoints to be reviewed, audited, or analysed.

Example log entry:
Decision ID: DAP-A9C8-2026
Decision type: Credit agreement
Outcome: Confirmed
Timestamp: 2026-03-10T15:37:22Z

Logs may be stored by:

- the platform
- the DAP service
- both parties

---

## 9. Reference user interface (non-normative)

DAP does not prescribe a specific user interface.

However, a checkpoint interface typically includes:

1. The commitment being confirmed
2. A short summary of implications
3. Legitimate alternative outcomes
4. Confirmation signals
5. A final commitment confirmation

Example reference screens are provided in the repository under:

`reference-ui/`

These examples illustrate one possible implementation.

---

## 10. Security considerations

DAP introduces a record of the context surrounding a commitment.

Implementations should consider:

- integrity of decision records
- tamper resistance
- secure transport of decision data
- privacy protection

Platforms should ensure that decision records cannot be altered after confirmation.

---

## 11. Privacy considerations

DAP is designed to record **decision context**, not personal data.

Implementations should avoid storing personally identifiable information where possible.

Decision receipts may be designed to function without exposing sensitive user information.

---

## 12. Versioning

DAP follows semantic versioning. 
MAJOR.MINOR.PATCH

Version changes indicate protocol evolution.

---

## 13. Status of this document

This specification describes **Draft v0.1** of the Decision Assurance Protocol.

The repository is intended to explore whether a lightweight standard for recording consequential digital decisions would be useful across digital platforms.

Feedback and discussion are welcome.

