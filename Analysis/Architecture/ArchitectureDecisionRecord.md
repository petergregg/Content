# Architecture Decision Records Format

## TITLE
Short description stating the architecture decision

## STATUS
Proposed, Accepted, Superseded

## CONTEXT
What is forcing me to make this decision?

## CONSEQUENCES
What is the impact of this decision?

## COMPLIANCE 
How will I ensure compliance with this decision?

## NOTES
Meta data for the decision (author, etc.)

# Architecture Decision Records Example
## ADR 1. Asynchronous Pub/Sub Messaging Between Bidding Services

## STATUS
Accepted

## CONTEXT
The Bid Capture Service, upon receiving a bid from an online bidder or from a live bidder via the auctioneer, must forward that bid onto the Bid Streamer Service and the Bidder Tracker Service. This could be done using asynchronous point-to-point(p2p) messaging, asynchronous publish-and-subscribe (pub/sub) messaging, or REST via the Online Auction API Layer.

## DECISION
We will use asynchronous pub/sub messaging between the Bid Capture Service, Bid Streamer Service, and the Bidder Tracker Service.

The Bid Capture Service does not need any information back form the Bid Streamer Service or Bidder Tracker Service. 

The Bid Streamer Service must receive bids in the exact order they were accepted by the Bid Capture Service. Using messaging and queues automatically guarantees the bid order for the stream. 

Using async pub/sub messaging will increase the performance of the bidding process and allow for extensibility of bidding information.

## CONSEQUENCES 
We will require clustering and high availability of the message queues.

Internal bid events will be bypassing security checks done in the API layer.

UPDATE: Upon review at the April 14th, 2020 ARB meeting, the ARB decided that this was an acceptable trade-off and no additional security checks would be needed for the bid events between these services.

## COMPLIANCE
We will use periodic manual code and design reviews to ensure that asynchronous pub/sub messaging is being used between the Bid Capture Service, Bid Streamer Service, and the Bidder Tracker Service.

## NOTES
Author: Peter Gregg

Approved By: ARB Meeting Members, 14 APRIL 2020

Last Updated: 15 APRIL 2022 by Peter Gregg
