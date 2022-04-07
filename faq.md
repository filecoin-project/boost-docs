# FAQ

Q: Is there a way to stop `boostd` daemon?\
A: You can use the regular Unix OS signals

Q: Is Boost compatible with the Lotus client? Can a client use `lotus client deal` to send a deal to Boost storage providers or do they have to use the boost client?\
A: Yes, boost should work with any client given it supports the storage market protocol / default standard of Filecoin network today.

Q: Does Boost provide retrieval functionality or a client still need to use the Lotus client for retrievals?\
A: Boost doesn't change anything about retrieval so far. We are working on new retrieval options.

Q: Can I specify another wallet address in Boost? For example, I have multiple addresses containing FIL+ DataCap and I need to switch between them when making deals.\
A: Not yet. We are working on adding this feature.

Q: Can Boost make verified deals?\
A: Yes, payments for deals can be made either from a regular wallet, or from DataCap. Deals that are paid for with DataCap are called `verified` deals.
