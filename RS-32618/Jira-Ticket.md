- [RS-32618](https://vikingtravel.atlassian.net/browse/RS-32618)

8

# RemoveExtensionsRequest API's RefundAmountDue parameter only returns an amount for Final status bookings

Key details

## Description

**Bug**: The RefundAmount returned in the RemoveExtensionsRequest API icurrently only returns a RefundAmount when the booking is in Final status prior to the shorex removal, and the removal caused there to be an overpayment on the booking

In order for the refund API to be the source of business rules on whether a booking should be refunded or not, we need a new parameter for **TotalRemovedExtnCost.**

- This parameter will need to sum up the Amount of all shorex removed for that booking and return that total. The 'Amount' is the final amount the guest pays (after vouchers, price adjustments, etc):
    

![image-20261022-171856.png](blob:https://vikingtravel.atlassian.net/7886389c-858f-4723-ac1f-b4fc102a5185#media-blob-url=true&id=8063b117-c1d2-43b1-b819-711efad820c1&collection=&contextId=1974928&width=1220&height=717&alt=image-20261022-171856.png&clientId=ddd92437-5aae-425a-8455-8dd82b12a453)

Sample in STG: **Booking: 9631977**

Remove Extension 213 of $89.00 for Pax 2, and 1 Included tour. Refund amount should be $89.00

![image-20261019-174129.png](blob:https://vikingtravel.atlassian.net/223eb217-e6bc-46bb-96b3-334c1162c396#media-blob-url=true&id=18113d4b-e684-4b14-b3e1-4028d27a7e26&collection=&contextId=1974928&mimeType=image%2Fpng&name=image-20261019-174129.png&size=114273&width=1315&height=679&alt=image-20261019-174129.png&clientId=ddd92437-5aae-425a-8455-8dd82b12a453)

**Payload:**

|**Request**|**Response**|
|---|---|
|`<RemoveExtensionsRequest> <MessageHeader> <MessageId>REMEXTREQ</MessageId> <Version>1.0</Version> <UserID>JLIN</UserID> <UserName>jennifer.llin@viking.com</UserName> <UICode>C</UICode> <GDSCode>CS</GDSCode> <AgencyId></AgencyId> <AgentIata></AgentIata> <EmailAddress>jennifer.lin@viking.com</EmailAddress> <SessionID></SessionID> </MessageHeader> <MessageBody> <JSonPayload>{ "Invoice": "9631977", "UserID": "JLIN", "Comment": "", "BypassCancelPenalties": false, "Extensions": [{ "Cruise": "OMA261207", "Code": "213" },{ "Cruise": "OMA261207", "Code": "204", } ] } </JSonPayload> </MessageBody> </RemoveExtensionsRequest>`|`-<RemoveExtensionsResponse> -<MessageHeader>  <MessageId>REMEXTRES</MessageId>  <Version>1.0</Version>  <UserID>FPAPAIOANU</UserID>  <UserName>franklin.papaioanu@viking.com</UserName>  <UICode>C</UICode>  <GDSCode>CS</GDSCode>  <AgencyId/>  <AgentIata/>  <EmailAddress>franklin.papaioanu@viking.com</EmailAddress>  <SessionID/> -<AdvisoryCode="0001">  <Comment/>  </Advisory>  <System>Evolution</System>  </MessageHeader> -<MessageBody>  <JSonPayload>{"Advisory":{"Code":"0001","Comment":""},"Invoice":"8963770","RefundAmountDue":0.0,"Extensions":[{"Cruise":"ALR260912","Code":"141"},{"Cruise":"ALR260912","Code":"170"}]}</JSonPayload>  </MessageBody>`|

**Refund Amount here is shown as $0.00**

![image-20261019-180328.png](blob:https://vikingtravel.atlassian.net/950e6afa-ca0e-4154-afde-d136ec1fc3b4#media-blob-url=true&id=3c03f22f-97ec-42f4-b556-90074e41ddc0&collection=&contextId=1974928&mimeType=image%2Fpng&name=image-20261019-180328.png&size=211474&width=1304&height=1030&alt=image-20261019-180328.png&clientId=ddd92437-5aae-425a-8455-8dd82b12a453)

**If I put the same booking in Final status and rerun the payload on extension 171 at a price of $198, it now calculates a Refund Amount.**

![image-20261019-180319.png](blob:https://vikingtravel.atlassian.net/29a1b871-7fc4-40ed-bf9b-85e681c9fe56#media-blob-url=true&id=7dfcc95a-80af-4c0b-82ab-53d329a12c52&collection=&contextId=1974928&mimeType=image%2Fpng&name=image-20261019-180319.png&size=153280&width=1293&height=811&alt=image-20261019-180319.png&clientId=ddd92437-5aae-425a-8455-8dd82b12a453)

## Business Justification / Impact

Add text

## Table Schema Changes

Add text

## Acceptance Criteria

**Testing should include:** Please verify that the API response has a new parameter ‘**TotalRemovedExtnCost**’. The parameter should return the sum of the cost of **all** removed shorex from the booking. All successful removals should be factored into this total, but if a row failed, that amount should not be added into this total.

- Validate removing paid extension(s) from a booking will accurately calculate the **TotalRemovedExtnCost**as the amount that should go back to the booking
    
- Validate vouchers applied to the removed shorex are correctly removed from the **TotalRemovedExtnCost**
    
- Validate if the shorex price was reduced at the time it was added, that the **TotalRemovedExtnCost**correctly pulls from the price the shorex was added at, not the cruise’s build price
    
- Validate if cancel penalties are not bypassed, and the shorex are in penalty at the time of removal, that no refund amount will be due, since shorex get penalized at 100% if they are under penalty
    
- Please validate the correct amount is returned regardless of booking statuses (except cancelled)
    
- Please regression test no changes were made to the other parameters in the response from ticket [RS-21108: New Service to Remove Shorex and Reprice the Booking (API Only)Closed](https://vikingtravel.atlassian.net/browse/RS-21108)
    

## Release Notes

The RemoveExtensionsRequest API has been enhanced to include a new parameter, TotalRemovedExtnCost, which accurately sums the final amounts of all removed shore excursions regardless of booking status, enabling consistent refund calculations beyond just Final status bookings.

## Test Cases

||RS-TC-3704|1|API returns TotalRemovedShorexCost for one successful removal|||

Step

Data

Expected result

---

1

Create or select a non-cancelled booking with one removable, paid shore excursion and record the amount charged when it was added.

-

The booking and recorded shorex amount are available.

2

Submit the supported API request to remove the shorex.

-

The shorex removal succeeds.

3

Inspect the API response.

-

The response contains TotalRemovedShorexCost.

4

Compare TotalRemovedShorexCost with the recorded amount due back to the booking.

-

TotalRemovedShorexCost equals the successfully removed shorex amount due back to the booking.

5

Retrieve or refresh the booking and repeat the relevant API retrieval.

-

The shorex remains removed and the returned removal result is consistent after refresh.

||RS-TC-3705|1|API sums multiple successful removed shorex rows|||
Step

Data

Expected result

---

1

Prepare a non-cancelled booking with multiple removable shore excursions and record each amount charged when added.

-

All shorex rows and their charged amounts are recorded.

2

Submit one request that removes all selected shorex rows.

-

Every selected row is removed successfully.

3

Inspect TotalRemovedShorexCost in the response.

-

The parameter is present.

4

Add the recorded amounts for all successfully removed rows and compare the sum with the response.

-

TotalRemovedShorexCost equals the sum of all successful removed-shorex amounts.

5

Confirm the response does not report only the first, last, minimum, or maximum row amount.

-

The response reports the full aggregate total.
||RS-TC-3706|1|Failed removal row is excluded from total|||
Step

Data

Expected result

---

1

Prepare a booking/request containing at least one shorex row that can be removed and one row that will fail validation or removal under supported test conditions.

-

The request contains both a successful candidate and a failing candidate.

2

Record the charged amount for each row.

-

Expected successful and failed row amounts are known.

3

Submit the removal API request.

-

At least one row succeeds and at least one row fails.

4

Inspect row-level outcomes and TotalRemovedShorexCost.

-

The response identifies the differing row outcomes and includes TotalRemovedShorexCost.

5

Compare the total with only the successfully removed row amounts.

-

TotalRemovedShorexCost includes successful removals and excludes every failed row amount.

6

Refresh the booking.

-

Successful rows remain removed; failed rows remain on the booking.
||RS-TC-3707|1|All removal rows fail|||
Step

Data

Expected result

---

1

Prepare a request in which every shorex row will fail removal under supported test conditions.

-

All selected rows are expected to fail.

2

Submit the removal request.

-

No shorex row is removed successfully.

3

Inspect TotalRemovedShorexCost.

-

The parameter is returned and does not include any failed-row amount.

4

Refresh the booking.

-

All shorex rows remain on the booking.
||RS-TC-3708|1|Voucher value is removed from refundable total|||
Step

Data

Expected result

---

1

Prepare a non-cancelled booking with a removable shorex that has a voucher applied; record the added price and applied voucher value.

-

The price and voucher value are known.

2

Remove the shorex through the API.

-

The removal succeeds.

3

Inspect TotalRemovedShorexCost.

-

The parameter is present.

4

Compare the response with the shorex amount after removing the voucher value.

-

TotalRemovedShorexCost excludes the voucher-applied portion and reflects the amount due back to the booking.

5

Refresh the booking and review the removed item result.

-

The shorex removal persists and the voucher is not retained as part of the returned removed-shorex cost.
||RS-TC-3709|1|Multiple removed shorex with mixed voucher usage|||
Step

Data

Expected result

---

1

Prepare multiple removable shorex rows, with vouchers applied to some rows and none applied to others; record added prices and voucher values.

-

The expected amount for each row is known.

2

Remove all rows in one supported API operation.

-

All selected rows succeed.

3

Calculate the expected total as each added price less its applicable voucher value.

-

The expected aggregate is available.

4

Compare the calculation with TotalRemovedShorexCost.

-

The response equals the aggregate after voucher values are removed only from their corresponding rows.
||RS-TC-3710|1|Reduced added price is used instead of cruise build price|||
Step

Data

Expected result

---

1

Prepare a booking where a shorex was added at a reduced price and record both the actual added price and the cruise build price.

-

The two prices are known and differ.

2

Remove the shorex successfully through the API.

-

The removal succeeds.

3

Inspect TotalRemovedShorexCost.

-

The parameter is present.

4

Compare the response with both recorded prices.

-

TotalRemovedShorexCost equals the price at which the shorex was added, not the cruise build price.

5

Refresh the booking.

-

The removal and returned amount remain consistent with the historical added price.
||RS-TC-3711|1|Reduced price and voucher are both applied correctly|||
Step

Data

Expected result

---

1

Prepare a shorex added at a reduced price with a voucher applied; record the reduced added price, build price, and voucher value.

-

All source amounts are known.

2

Remove the shorex successfully.

-

The removal succeeds.

3

Compare TotalRemovedShorexCost with the reduced added price less the voucher value.

-

The response uses the reduced added price as the basis and excludes the voucher value; it does not use the build price.
||RS-TC-3712|1|In-penalty shorex with penalties not bypassed returns no refund|||
Step

Data

Expected result

---

1

Prepare a non-cancelled booking with shorex currently under penalty and ensure cancel penalties are not bypassed.

-

The shorex is under penalty and bypass is not enabled.

2

Record the shorex charged amount.

-

The original amount is known.

3

Remove the shorex through the API.

-

The removal is processed under the applicable penalty.

4

Inspect TotalRemovedShorexCost.

-

No refund amount is due in TotalRemovedShorexCost because the shorex is penalized at 100%.

5

Refresh the booking and inspect the result.

-

The removal outcome persists and no refundable removed-shorex cost appears.
||RS-TC-3713|1|Penalty bypass alternate flow|||
Step

Data

Expected result

---

1

Using a role that is authorized in the test environment, prepare an in-penalty shorex removal with cancel penalties bypassed.

-

The authorized bypass condition is active.

2

Submit the removal request and record the row outcome.

-

The API processes the request according to the configured bypass behavior.

3

Compare TotalRemovedShorexCost with the successful row outcome and amount due back to the booking.

-

Only a successful removal amount due back to the booking is included; failed rows remain excluded.

4

Repeat with a role that is not authorized to bypass penalties, if role enforcement is exposed by the supported workflow.

-

The unauthorized attempt cannot silently receive bypass treatment; its amount is included only if the row is successfully removed and refundable under the actual outcome.
||RS-TC-3714|1|Supported non-cancelled booking statuses return correct total|||Step

Data

Expected result

---

1

Identify each supported booking status other than Cancelled in the test environment.

-

The supported non-cancelled status set is available.

2

For each status, prepare an equivalent booking with the same removable shorex price conditions.

-

Comparable test data exists for each status.

3

Submit the removal request for each booking.

-

Each request returns its actual row-level outcome.

4

Compare TotalRemovedShorexCost with the sum of successful refundable removals for each status.

-

The correct amount is returned regardless of the non-cancelled booking status; failed rows are excluded.

5

Refresh each booking.

-

Each persisted booking state matches its API result.
||RS-TC-3715|1|Cancelled booking is excluded from supported-status assertion|||
Step

Data

Expected result

---

1

Prepare or identify a cancelled booking containing relevant historical shorex data.

-

A cancelled booking is available.

2

Submit the supported API request, if the endpoint accepts the request for a cancelled booking.

-

The actual API response is captured.

3

Verify no amount from a failed or rejected removal is added to TotalRemovedShorexCost.

-

Any unsuccessful cancelled-booking row contributes nothing to the total.

4

Confirm the result is not used to assert AC coverage for supported booking statuses.

-

Cancelled remains outside the AC statement that requires amount consistency across statuses.
||RS-TC-3716|1|Zero refundable amount is represented correctly|||
Step

Data

Expected result

---

1

Prepare a removal scenario whose successful outcome has no amount due back, such as the AC-defined 100% penalty case with penalties not bypassed.

-

The expected refundable amount is zero.

2

Submit the removal request.

-

The request is processed.

3

Inspect TotalRemovedShorexCost.

-

The parameter represents a zero refundable total and does not reuse a prior or source price amount.

4

Repeat the retrieval or response validation after refresh.

-

The zero-total result remains consistent.
||RS-TC-3717|1|Decimal precision across multiple successful removals|||
Step

Data

Expected result

---

1

Prepare multiple removable shorex rows with valid fractional currency amounts as supported by the system and record the charged amounts.

-

The expected precise sum can be calculated from the recorded amounts.

2

Remove all rows successfully.

-

All selected rows succeed.

3

Compare TotalRemovedShorexCost with the exact sum of the recorded charged amounts after applicable voucher deductions.

-

The returned total matches the booking amounts without dropping a successful row or introducing an incorrect monetary total.
||RS-TC-3718|1|Response regression for removal result details|||
Step

Data

Expected result

---

1

Capture the established removal response elements used by the existing client or test harness before executing the scenario.

-

The expected existing response contract elements are identified.

2

Execute a successful shorex removal.

-

The API responds successfully.

3

Verify TotalRemovedShorexCost is present and validate the existing response elements used by the client.

-

The new parameter is available and the existing relied-upon removal response elements remain available with their expected values.

4

Execute a mixed success/failure removal.

-

The API returns the actual row outcomes.

5

Verify the total remains consistent with successful rows while existing row outcome information remains usable.

-

The added total does not prevent validation of existing row-level outcomes.
||RS-TC-3719|1|RS-32618 RemoveExtensionsRequest API's RefundAmountDue parameter only returns an amount for Final status bookings|||