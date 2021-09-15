# Messages

Messages are one of two primary objects handled by a module in Cosmos. The other is queries. As you might expect, queries inspect the module state and are alway read-only and messages inform the state and have the potential to alter the state, also known as state mutating.

In Cosmos, a transaction contains one or more messages for the module to process after the transaction is included in a block by the consensus layer.

## Flow

Transactions containing one or more valid messages are serialized and confirmed by the Tendermint consensus. Recall that Tendermint is agnostic about interpretation of the transactions. When a transaction is included in a block, it is confirmed and finalized with no possibility of chain reorganization or cancellation.

The confirmed transaction is passed to the Cosmos SDK for interpretation. The `BaseApp` you use to begin module development of your custom modules attends to the first stages of interpretation. It decodes each message contained in the transaction.

Each message is routed to the appropriate module via `baseApp’s` `MsgServiceRouter`. Each module has its own `MsgService` that processes each received message.

## MsgService

Although it is technically feasible to proceed to create a novel `MsgService`, the recommended approach is to define `Protobuf` `Msg` service. Each module will have exactly one Protobuf Msg service defined in `tx.proto` and there will be a RPC service method for each message type in the module. Implicitly, the ProtoBuf message service defines the interface layer of the state mutating processes contained within the module.

### Example MsgService

```
// Msg defines the bank Msg service.
service Msg {
  // Send defines a method for sending coins from one account to another account.
  rpc Send(MsgSend) returns (MsgSendResponse);

  // MultiSend defines a method for sending coins from some accounts to other accounts.
  rpc MultiSend(MsgMultiSend) returns (MsgMultiSendResponse);
}
```

In the example above we can see that:

* Each Msg service method has  exactly one argument, which must implement the sdk.Msg interface and a Protobuf response
* The standard naming convention is to call the RPC argument `Msg<service-rpc-name>` and the RPC response `Msg<service-rpc-name>Response`

## Code Generation with Cosmos SDK

Cosmos SDK uses Protobuf definitions to generate client and server code:

* The `MsgServer` interface defines the server API for the `Msg` service and its implementation is described as part of the [Msg services](https://docs.cosmos.network/master/building-modules/msg-services.html) documentation.
* Structures are generated for all RPC request and response types.
* Implementation of a module Msg service: (https://docs.cosmos.network/master/building-modules/msg-services.html)[https://docs.cosmos.network/master/building-modules/msg-services.html]
* Method to define messages using Msg services - Amino LegacyMsg: (https://docs.cosmos.network/master/building-modules/messages-and-queries.html#legacy-amino-legacymsgs)[https://docs.cosmos.network/master/building-modules/messages-and-queries.html#legacy-amino-legacymsgs]

## Long-running exercise

See the section about transactions. Think later about how to divide into the 2 sections.