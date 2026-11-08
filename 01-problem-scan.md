# Phase 1
## Idea 1: VinFast parts forecasting and supplier matching
Lens: Time-consuming

Today, supply-chain planners probably spend hours by hand going through production plans, stock levels and supplier quotes to decide what to order and from whom.

Using AI assisted system to forecast part demand and match orders to suppliers ahead of production. Keep production going smoothly and parts always in stock.

## Idea 2: Vinhomes urgent-ticket prioritization
Lens: Stakeholder pain

Rank resident tickets by urgency so critical issues (no water, broken appliance,...) reach staff before routine ones (moving, how to subscribe to services,..)

## Idea 3: VinMec Issurance Claim Check
Lens: Repetitive

Insurance claim checks: staff check each claim’s documents by hand against each insurer’s rules. Doing this by hand takes too long, and rejected claims may delay payments and go into disputes. Using an AI system to assist with this process may save-time of upto 10-20min for each cases.

## Idea 4: VinMec Appointment Hotline improvement
Lens: Ai-upgrade

Appointment hotline: booking is by phone, with long waits and generic replies. We can implement an AI assisted system, that the user can leave a message to or point the customer to other channels that can make appointment with ease. Reduce the customer waiitng time that can take up to 5-10min and the customer may hangs up.

## Idea 5: 
Lens: Repetitive

Lost items: a passenger reports something left in the car; support staff find the trip, call the driver and arrange the return
Pains: about 10–15 min per case; hundreds of cases a week in big cities

By using an AI assisted system, that automatically access the car camera in the alotted time frame where the customer take off from the car, it may spot items in the backseat of the car to confirm that the customer has left somethings behind -> arrange the return with the driver in the app immediately to save round trip for the driver. 

## 📝 List bài toán của tôi (Problem Scan Summary)

| # | Subsidiary (VinFast/Xanh SM...) | Lens | Mô tả ngắn bài toán |
|---|----------------------------------|------|---------------------|
| 1 | VinFast | Time-consuming | Forecast part demand and match orders to suppliers ahead of production, so parts are always in stock |
| 2 | Vinhomes | Stakeholder Pain | Rank resident tickets by urgency so critical issues (no water) reach staff before routine ones (moving-truck parking) |
| 3 | Vinmec | Repetitive | Staff check each insurance claim's documents by hand against each insurer's rules; slow, and rejected claims delay payment |
| 4 | Vinmec | AI-upgrade | The phone booking hotline has long waits and generic replies; AI takes messages or redirects patients to easier booking channels |
| 5 | Xanh SM | Repetitive | Lost items: AI checks the car camera at drop-off time to confirm an item was left, then arranges the return with the driver in the app |

---

# Phase 2 — QUICK-ASSESS

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                       │
│                                                             │
│ Bài toán (1 câu): Supply chain managers spend 30–60 min     │
│   manually reading production plans and comparing parts     │
│   needs with stock before placing orders.                   │
│ Công ty thành viên: [x] VinFast  [ ] Xanh SM  [ ] Vinhomes  │
│                     [ ] Vinmec   [ ] Khác (Ghi rõ)________  │
│                                                             │
│ Ai đang đau (Actor)? Supply chain managers                  │
│                                                             │
│ Workflow thủ công hiện tại (3-5 bước):                      │
│   1. Check production plan ──> 2. Confirm total parts       │
│   needed ──> 3. Compare with stock ──> 4. Place order       │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Steps 1 & 3                │
│   (⏱ ~30–60 phút/lượt per production batch, estimate)       │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Steps 1–3: extract    │
│   production details, estimate parts needed, compare with   │
│   stock; manager makes the final order decision.            │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│   Reduce time from reading the production plan to the      │
│   order decision: 30–60 min ──> 10 min per production batch │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent │
│   LLM reads messy production plans and pulls out the parts  │
│   needed.                                                   │
└─────────────────────────────────────────────────────────────┘
```

> **Focus:** this card covers only parts-needs forecasting. Supplier matching is a second problem in the same workflow and is left out for now.
> **Assumption:** production plans arrive as messy, unstructured text. VinFast factories may already use software for this; not verified.

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                       │
│                                                             │
│ Bài toán (1 câu): Insurance desk staff spend up to 1 hour   │
│   per claim reading insurance contracts and checking        │
│   whether the evidence matches the contract details.        │
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [ ] Vinhomes  │
│                     [x] Vinmec   [ ] Khác (Ghi rõ)________  │
│                                                             │
│ Ai đang đau (Actor)? Hospital insurance desk staff          │
│                                                             │
│ Workflow thủ công hiện tại (3-5 bước):                      │
│   1. Receive claim documents ──> 2. Check against the       │
│   insurer's rules ──> 3. Confirm the evidence for the       │
│   claim ──> 4. Pass the confirmation to Finance for payment │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Steps 2–3: reading the     │
│   insurance contract and checking whether the evidence      │
│   matches its details (⏱ up to ~60 phút/lượt at peak,       │
│   estimate)                                                 │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Steps 2–3: find and   │
│   read the right evidence, point staff to where to read and │
│   what to read (with sources), and suggest similar past     │
│   cases (only where evidence was found; outcome hidden).    │
│   Less information overload; staff make the decision.       │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│   Reduce time per insurance claim: peak 1 hour ──> peak     │
│   10 min                                                    │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent │
│   LLM reads scanned and handwritten documents and builds a  │
│   knowledge base of where each contract detail lives, so    │
│   every answer comes back with its source.                  │
└─────────────────────────────────────────────────────────────┘
```

> **Operational boundary:** AI must **never** accept or reject a claim, or suggest accepting or rejecting it. It may only suggest which documents to read, where to read them, and what to read in them. It must never plant the idea of rejecting a claim, because that would pass the AI's own bias to staff. The human is the only decider. For the same reason, similar past cases show **only where the evidence was found**, never whether those claims were accepted or rejected.

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                       │
│                                                             │
│ Bài toán (1 câu): Patients wait 5–10 min on the Vinmec      │
│   appointment hotline before they can book, and many hang   │
│   up.                                                       │
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [ ] Vinhomes  │
│                     [x] Vinmec   [ ] Khác (Ghi rõ)________  │
│                                                             │
│ Ai đang đau (Actor)? Patients waiting on the phone          │
│                                                             │
│ Workflow thủ công hiện tại (3-5 bước):                      │
│   1. Patient calls the hotline ──> 2. Waits in the queue    │
│   (hang-ups happen here) ──> 3. Staff answer and take the   │
│   details ──> 4. Staff check the schedule and book ──>      │
│   5. Staff confirm with the patient                         │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Step 2: waiting in the     │
│   queue before they can pass on their message               │
│   (⏱ ~5–10 phút/lượt, estimate)                             │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Step 2: when the line │
│   is long, an auto-response offers other booking channels;  │
│   patients who stay leave a voice message right away. AI    │
│   breaks down the voice message and passes the key details  │
│   to staff. After staff finalize the appointment, AI sends  │
│   the patient a text confirmation.                          │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│   Wait before leaving details: 5–10 min ──> 10–15 s         │
│   Hang-up rate: 10% ──> 1%                                  │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent │
│   LLM transcribes and summarizes voice messages; staff do   │
│   the booking, so no Agent is needed.                       │
└─────────────────────────────────────────────────────────────┘
```

> **Operational boundary:** AI must never add or leave out crucial information when summarizing a voice message for staff. Unclear or uncertain recordings are passed to a human for review.
