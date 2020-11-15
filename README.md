To test project:
    - truffle test

1. Let's say you have already implemented a standardized ERC-20 smart contract for some token. 
Now you want to add the possibility of securely updating the balances between any two users, off-chain using state channel technique. 
Can you write simple JavaScript/TypeScript/NodeJS code that generates state channel receipts (signed by both users) and one Solidity function that validates that receipt and updates the balances accordingly? 
Note that these 2 users can update their balances off-chain multiple times before they actually want to settle it on-chain.
    The javascript function to create the receipt is inside the ./utilities/paymentChannel.js class.
    In the test folder there are some examples to help understand how this smart contract works.
    Being a purely demonstrative project I have hardcoded some data inside the code for simplicity.
    From what I understand in the description of this exercise the receipt must be signed by both users at the same time, is this true?
    Since only 2 addresses are involved in this exercise, every time one has to validate an operation, it need to have the signature of the other one.
    An Address doesn't need to prove to himself that he has his own signature, so I don't think the contract should be signed by both Address at the same time, but it only need to be signed by the sender.
    I have created the signPayment (JS) function which can be used by both address to generate a receipt to send to the other party.
    The close function(Solidity) allows both contracts to close the channel.
    If one contract collects a receipt with an outdated nonce, the other contract can always cash a receipt with a major nonce.
    The nonce is incremental and it is not possible to submit receipts with nonce smaller than the last one presented.
    If we want to generate a receipt signed by both the addresses at the same time, we can call the signPayment function twice, once from each address, using the output of the first signature as the input (message parameter) of the second signature.
    But this would make sense to be done if it were necessary to have a third address that need to validate the other two.
    Alternatively, to sign a contract from both addresses, it would be possible to use a multi-sig wallet created by both addresses.

2. What happens if one of the users present to the smart contract one of the older version of the receipt (that reflects bigger balance for that user then it would otherwise reflect if he presented the most recent receipt)? 
How we can prevent situations like that from happening or how we can make sure even if it happens the other honest user can always overwrite/challange the balance with the more recent/accurate receipt?
    Inside each receipt there is a nonce.
    Whenever a user claims a receipt, the nonce is incremented.
    A user cannot claim a receipt with a lower nonce than the last claimed.
    If a user claims a receipt with a lower nonce than the last one, this is not fulfilled.
    Alternatively, we could insert a flag in the contract that requires validation by the sending user before we can collect the contract.
    In the function used to close the contract we could insert an expiration date variable, if the sender does not validate the operation before the deadline the receiver can claim the money.
    This kind of operatione would require more transactions cost than the current ones, (2 operations from the receiver and 1 operation from the sender), this means a higher price in terms of gas.

3. Theoretical question, give me all your ideas you can find for this problem: assuming the user needs to put also the actual timestamp to the receipt, how another party (another user) can validate that timestamp was really actual/current? How we can make sure the margin error of that validation is smallest possible? Any ideas, even most crazy ones count :-)