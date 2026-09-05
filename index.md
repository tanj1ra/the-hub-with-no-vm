---
layout: default
title: The Hub With No VM
description: How AtomOne plans to remain a minimal hub while gno.land supplies its programmability.
---

# The Hub With No VM

*How AtomOne plans to remain a minimal hub while gno.land supplies the programmability it deliberately leaves out.*

## In this article

- [What AtomOne is borrowing](#what-atomone-is-borrowing)
- [The wire is already live](#the-wire-is-already-live)
  - [What the audit caught](#what-the-audit-caught)
  - [Built on IBC v2](#built-on-ibc-v2)
  - [It works, and it has for a while](#it-works-and-it-has-for-a-while)
- [What the gno.land launch unlocks](#what-the-gnoland-launch-unlocks)
- [The part that has not been built](#the-part-that-has-not-been-built)
- [What comes next](#what-comes-next)

---

Here is everything the AtomOne blockchain can do.

It holds accounts and moves balances. It stakes, delegates, slashes, and pays rewards. It runs a
governance system elaborate enough to have a written constitution, two DAOs with different
powers, and a proposal deposit that adjusts itself based on demand. It mints PHOTON by burning
ATONE. It speaks IBC.

That is the whole list. The complete module set is auth, bank, mint, staking, slashing,
distribution, gov, photon, coredaos, epochs, evidence, authz, feegrant, params, vesting, and the
IBC stack.

Read it again and notice what is missing.

Search AtomOne's source for `wasm` and you get nothing. Search for `evm` and you get nothing.
Check `go.mod` and there is no virtual machine dependency of any kind. AtomOne cannot execute a
smart contract. Not "not yet," not "pending an upgrade." There is no module to enable and no flag
to flip. The chain has no capacity to run user-supplied code, and nothing in the repository
suggests anyone is building one.

For a Layer 1 in this decade that is close to heresy. The entire competitive logic of the last
five years has been that a chain is a platform, that a platform needs developers, and that
developers need somewhere to deploy. Chains that launched without a VM raced to add one. Chains
that had one raced to make it faster, cheaper, or compatible with somebody else's.

AtomOne looked at that and declined.

The official phrasing for this is hub minimalism, and AtomOne's founding FAQ states the position
without hedging: the chain preserves hub minimalism precisely so that "innovative solutions, such
as smart contracts on ICS-secured shard chains" can exist somewhere else. The hub does consensus,
security, governance and money, holds as little attack surface as it can, and lets programmability
live in a state machine whose failures cannot become its own.

Jae Kwon, whose company builds the chain, puts the token side of it more bluntly still: "ATOM and
ATONE's only utility should be staking, and other utilities should be offered by ICS hosted
applications."

If that argument sounds familiar, it should. The Cosmos Hub spent years circling it. Ethereum
arrived at the same conclusion from the opposite direction, pushing execution outward into
rollups because the base layer could not carry it. Both got there by retrofitting. AtomOne simply
started there, which is easy to mistake for having built less.

But a hub with no VM has an obvious problem, and it is not a subtle one. Programmability is where
the users are. A chain that cannot host an application is a chain nobody has a reason to visit.
Minimalism is a defensible engineering position and a terrible product position, unless you have
somewhere specific for the applications to go.

AtomOne has somewhere specific, and a model for how it fits together.

The model is called the three layer cake, and each layer depends on the one beneath it. Layer 3
is the application, the dapp somebody actually uses. Layer 2 is the smart contract platform it
runs on. Layer 1 is the hosting provider that supplies consensus and security to the platform
above. AtomOne is not trying to be all three. It is trying to be Layer 1, and it says so.

Layer 2, for now, is gno.land.

And this is where it stops looking like two chains negotiating and starts looking like one
design. Gno.land is built by All in Bits, the company whose copyright notice still reads *dba
Tendermint Inc* in the license files of the consensus engine it originally built. All in Bits
also commissioned the audit of AtomOne's latest release. The same organization is building the
minimal hub and the virtual machine it intends to borrow. Tendermint2, the engine gno.land runs,
is their own fork of their own original.

So the question is not whether these two chains will cooperate. It is what exactly AtomOne is
borrowing, and whether the thing on the other end is real.

## What AtomOne is borrowing

Gno.land is a chain whose entire purpose is the thing AtomOne refuses to do.

Its virtual machine runs Gno, a language that is Go in almost every respect that matters. Not
Go-like, not Go-inspired, not a DSL with familiar syntax. Contracts are written with Go's type
system, Go's standard library conventions, and Go's tooling, then executed deterministically on
chain. The pitch is not subtle and it does not need to be: there is an enormous population of Go
developers, and none of them have to learn Solidity to use this.

The second design choice matters more than it first appears. Gno.land publishes contract source,
not bytecode. A deployed realm, which is what gno.land calls a stateful contract, can be read on
chain as the code its author wrote. There is no verification step, no trusting an explorer's
claim that this bytecode was compiled from that repository. The source is the artifact. For a
chain built by people whose stated concern is verifiability, that is not a convenience feature,
it is the argument.

And it exists. This is the part worth being precise about, because "the VM chain we plan to use"
describes a lot of vapour in this industry.

Gno.land is running right now, in three distinct forms, and blurring them produces nonsense.
There is `pearl-1`, the current testnet, released in August. There is `gnoland1`, a live network
the project calls Beta Mainnet, which has produced over three and a half million blocks and hosts
the GNOT token distribution, though ordinary transfers on it remain locked. And there is a full
mainnet, which has not launched. Anyone claiming gno.land is live and anyone claiming it is
unlaunched can both cite something true, which is why the distinction has to be stated rather
than assumed.

What is unambiguous is that people are building on it, and the clearest evidence is what happens
when the chain gets destroyed.

Pearl launched on the 27th of August with nothing carried over. Balances reset to zero, every
deployed contract gone, every registered name released. Eight days later it holds 296 contracts.

Not eight days of core team demos, either. The wrapped-GNOT contract has been called 9,638 times
by 1,143 distinct addresses. The message boards have 582 separate callers, the on-chain blog 575,
the user registry 230, the naming system 228. GnoSwap redeployed its entire stack, pool, router,
staker, position, governance, launchpad and NFT contracts, plus eight test tokens. A DAO runs as
contracts rather than as a chain module. There are token factories, NFT markets, a name service
already on its fourth revision, and a long tail of games and experiments.

All of it is testnet. The pools hold seeded liquidity, the GNOT came from a faucet, and none of
the balances are worth anything. That is the correct state for a chain that has not launched, and
it means none of these numbers are evidence of economic traction. Anyone citing them as such is
selling something.

What they are evidence of is a developer base with muscle memory. Gno.land has torn down and
rebuilt its testnet four times since June, and each time the ecosystem reassembles itself within
days. That is a much harder thing to manufacture than a TVL figure, and a better predictor of
what happens at mainnet than any number denominated in a token nobody can sell yet.

Then there is the part that gives the whole arrangement away.

When gno.land computed its token distribution, 23.1% of the total GNOT supply was allocated to
holders of ATONE and PHOTON, based on a snapshot of AtomOne's chain state at block 6,439,117 in
December 2025. Not a partnership allocation. Not an ecosystem fund. Close to a quarter of the
supply, handed to the holders of a different chain's tokens, calculated from that chain's own
ledger.

That is not two projects agreeing to work together. That is one project treating another
project's holders as its own constituency, before a single packet had crossed between them. The
communities were merged by design, in the token math, well ahead of the technology.

So AtomOne's bet is less exotic than it sounds. It is not renting programmability from a
stranger. It is declining to run a VM in its own state machine while a sister chain, built by the
same company, runs one deliberately, and has already handed a quarter of its supply to AtomOne's
holders.

It is also worth being accurate about how exclusive that arrangement is, because it is easy to
overstate. The primary source is careful here: gno.land and AtomOne are described as
"complementary and symbiotic," with the division put as "gno.land solves the smart contract
problem for all VaaS hubs, AtomOne solves the scaling problem for all smart contract platforms."
Both halves are plural on purpose. The same document says outright that "gno.land is just one of
many future smart contracting platforms, and won't be the last to scale on AtomOne," and the
hosting model is designed to accept any application that speaks ABCI, which includes EVM and
CosmWasm chains.

So this is not a marriage. Gno.land is the first tenant of a building meant to hold many, and it
happens to be built by the landlord. That is a weaker dependency than it first appears, and a
more durable design.

Which leaves exactly one thing that has to be true for any of this to work.

The two chains have to be able to talk. Not in principle, not in a roadmap diagram, but in
production, with cryptographic proof, over a connection that neither side has to trust the other
to operate honestly.

That connection exists. It has been carrying packets for months, across four generations of
testnet, over a light client that had to be invented from scratch because nothing in existence
could do the job.

## The wire is already live

Gno.land is not a normal Cosmos chain, and that is what makes this interesting.

It runs Tendermint2, a fork that split from Tendermint years ago and was deliberately kept apart
from the Cosmos SDK. Its blocks are not CometBFT blocks. Its headers carry different fields,
hashed in a different order. So when AtomOne decided it wanted to talk to gno.land, the answer
was not "configure a relayer."

Ibc-go ships four light client types. One for solo machines, one for Tendermint chains, one for
WASM-hosted clients, one for a chain talking to itself. Not one of them can verify a Tendermint2
header. There was nothing to configure, because the thing AtomOne needed did not exist.

There is a quiet irony in that. Tendermint2 is All in Bits' fork of the consensus engine All in
Bits originally built, the one whose copyright notice in these files still reads *dba Tendermint
Inc*. The company ended up having to write a light client to read its own chain, because its own
fork had drifted far enough from the original that the standard Cosmos tooling could no longer
parse the blocks. Divergence has a cost, and someone eventually pays it in code.

So they wrote it. `modules/10-gno` is a fifth client type, out of tree, claiming a type string
nobody else uses. AtomOne took ibc-go's `07-tendermint` client, the code responsible for proving
another chain really produced the block it claims to have produced, and rebuilt its verification
around gno's header format. The pull request introducing it told reviewers to evaluate it by
diffing against the original. That is the most honest possible description: the standard client,
taken apart and reassembled around a different consensus engine.

AtomOne now maintains a light client that exists nowhere else in the ecosystem, for a consensus
engine exactly one chain in the world runs.

It is worth being clear about what that buys, because "bridge" is a word with a bad reputation
and most of it is deserved. The bridges that have lost hundreds of millions of dollars work by
appointing a group of operators to watch one chain and vouch for it on another. You trust the
operators. When the operators are compromised, the money leaves.

This is the other kind. Neither chain vouches for anything. AtomOne verifies gno.land's blocks by
checking validator signatures against a header chain it tracks itself, and gno.land does the same
in reverse. There is no operator set to compromise, and no privileged party who could authorize a
withdrawal that did not happen. Relayers move packets around, but a relayer cannot forge one, and
a relayer going offline stalls the bridge rather than draining it.

That is also why a bug in the light client is a serious matter rather than an annoyance. The
light client is the entire trust model. Break it and you have not broken a feature, you have
broken the reason anyone should believe the other chain at all.

### What the audit caught

Oak Security reviewed AtomOne's entire v4 changeset and returned fifty findings. Exactly one was
Critical, and it lived here.

Every consensus state the client stored carried an internal tag reading `10-gno`. Every consensus
state it derived from an incoming header left that tag empty, because one constructor set it and
the other did not. The misbehaviour checker compared the two with a deep equality check. It never
matched. Not for forged headers. Not for valid ones either.

Sit with what that means. When that comparison fails, the client concludes it has seen two
conflicting versions of history at the same height. That is the signature of validators
equivocating, and the correct response is to freeze. So anyone could permanently freeze the
bridge by taking a header the chain had already accepted and submitting it a second time. No
forgery. No stolen keys. No cost beyond gas. Resubmit something true and the bridge stops until
governance intervenes.

Three Major findings sat beside it. Equivocation detection read the wrong field, comparing each
block's parent hash instead of the hash validators actually signed, so two conflicting blocks at
the same height always looked identical and no double-signing could ever be reported. The
skip-ahead verifier looked validators up by their position in a list rather than by address,
checking signatures against the wrong keys whenever the validator set changed between two
heights. And the packet encoding had no way to represent a validator who simply did not sign,
which, since nearly every real gno.land block has one, meant authentic headers could not survive
conversion at all.

All four were fixed before release. Two hardening fixes landed after, including a guard against a
validator set with negative voting power, which would have made the two-thirds signing threshold
negative and therefore satisfiable by a single signature.

None of it reached production. That is the point. A bridge is a place where one chain agrees to
believe another chain's claims about itself, and the whole arrangement rests on the believing
being done correctly. This code was written, audited, broken, and repaired before it was asked to
hold anything.

### Built on IBC v2

The other notable choice is the protocol version.

In IBC as most people know it, connecting two chains is a handshake ceremony. Create a light
client, open a connection on top of it, open a channel on top of that, each step a multi-message
exchange both chains complete in lockstep. IBC v2, called Eureka, throws out the middle layers.
No connections. No channels. Packets reference a light client directly and route by client ID.

Eureka is not exotic. It has been live on the Cosmos Hub for a while, and Injective, dYdX, MANTRA
and Babylon are among the chains that have adopted it. But it has been adopted for almost exactly
one purpose, which is reaching Ethereum. The proportions make the point. Of the 1,513 IBC light
clients on the Cosmos Hub today, 1,504 are classic Tendermint clients. Nine are not. Cosmos still
talks to Cosmos the old way.

AtomOne and gno.land are doing something else with it. This is not an Ethereum on-ramp. It is two
chains in the same neighbourhood choosing the newer protocol for a link between themselves, which
is rare enough that the tooling has not caught up. Point a conventional IBC explorer at AtomOne's
testnet, ask it for channels to gno.land, and it reports none. Completely true, and entirely
beside the point. It is looking for a layer this bridge does not use.

You can see the fingerprint in the data. The ATONE voucher on gno.land carries the trace
`transfer/07-tendermint-2/uatone`, and that middle component, where classic IBC would put a
channel ID, is a client ID instead. The bridge announces its protocol version in the denomination
of its own tokens.

### It works, and it has for a while

Thirty-three packets have crossed. Twenty-two out of AtomOne, eleven back, spread across four
generations of gno.land testnet as that chain reset and renamed itself: `test-13`, then
`topaz-1`, then `sapphire-1`, now `pearl-1`. The heaviest traffic ran on `test-13`. Pearl carries
only four so far, because Pearl was released in August and is simply new.

Every outbound packet completed its round trip. Not one is sitting unresolved on any client,
across the bridge's entire history.

An acknowledgement is not a claim. It is the receiving chain's confirmation, relayed back and
committed into the sender's own state. AtomOne's testnet holds eleven of them. That is eleven
times gno.land verified an AtomOne packet against its own light client, accepted it, wrote a
receipt, and AtomOne verified that receipt in turn.

The tokens are worthless, and they should be. Gno.land has no mainnet, so everything crossing
this bridge is testnet supply moving between testnet chains. What is being proven is not value
transfer. It is that the machinery holds, repeatedly, across four chain resets, on a light client
someone had to invent.

And there is a detail in how the two halves were built that says something about both chains.
AtomOne's side is a Go module compiled into the chain binary, because AtomOne has no virtual
machine and a light client has nowhere else to live. Gno.land's side is `r/aib/ibc/core`, a
realm, which is to say a smart contract, running inside the VM like anything else.

The same bridge. Consensus-layer code on one end, an application on the other.

That asymmetry is not engineering taste. It is this whole argument, showing up in the shape of
the wire.

## What the gno.land launch unlocks

AtomOne has already done its half, and you can check it in one query.

When the v4 upgrade executed on mainnet in July, the handler did something small and easy to
miss. It appended `10-gno` to the chain's list of permitted IBC client types. That parameter is
governance-controlled, it is consensus state, and it is live on `atomone-1` right now. AtomOne's
mainnet is explicitly configured to accept light clients for a chain running Tendermint2.

Ask that same mainnet how many such clients exist and the answer is zero.

That gap is the whole situation in miniature, and it is not neglect. There is nothing for a
mainnet client to point at. Gno.land's mainnet does not exist yet. AtomOne's production chain is
holding a door open for a chain that has not been born, and it has been holding it since July.

What comes through that door is worth being concrete about, because "interoperability" is the
kind of word that can mean nothing.

Today ATONE has exactly two uses. You can stake it, and you can govern with it. PHOTON has one:
it pays for gas, and the only way to obtain it is by burning ATONE in a one-way conversion. These
are not limitations of the token design, they are limitations of the venue. There is nothing else
to do with a token on a chain that cannot run code. No lending market can hold it, because there
are no lending markets. No pool can price it, because there are no pools.

Move those same assets into a virtual machine and they stop being single-purpose. ATONE becomes
something a contract can hold, collateralize, or pool. PHOTON becomes particularly interesting,
because a fee token whose supply can only shrink relative to ATONE is a genuinely unusual asset,
and it has never been anywhere it could be traded against anything by a program rather than an
order book. Gno.land already has a working DEX with real pools. The bridge already knows how to
carry a voucher. Neither of those facts does anything for AtomOne until there is a mainnet on the
other side.

This produces a situation that should be stated plainly rather than dressed up. **AtomOne's next
significant milestone is not an AtomOne release.** The chain can ship upgrades, tune its
governance, adjust its validator economics, and none of it changes the programmability story.
That story advances when gno.land launches, and not before.

For an L1 that is a strange dependency to carry, though it is a smaller one than it looks. The
hosting model is meant to accept any chain that speaks ABCI, so nothing about the architecture
requires gno.land specifically to be the platform that proves it. What gno.land provides is the
first real tenant, and first tenants matter enormously, because an empty building and a building
with one occupant are judged very differently. AtomOne is waiting on a demonstration rather than
on a dependency, and the same company controls the calendar. But calendars slip, and the honest
version of this section says so.

The waiting looks nearly over, and the evidence for that is in the release notes rather than in
anybody's announcements.

Gno.land has shipped four separate testnets in ten weeks: `test-13` in mid June, `topaz` in July,
`sapphire` in early August, `pearl` at the end of it. Each one a fresh chain rather than an
upgrade, each starting from zero balances and zero state. That cadence is a team converging, not
exploring.

The contents are more specific than the cadence. The headline addition in Pearl is genesis
vesting accounts: linear unlock schedules, cliff schedules, a future start date, and one schedule
deliberately arranged to straddle genesis itself, shipped with ten test accounts covering the
full matrix. Nobody builds that for a throwaway network. You build it when you are rehearsing a
genesis that will carry a real token distribution with real lockups. It landed alongside three
fixes for coins being lost or over-authorized in the send path, a governance allowlist lockdown,
namespace enforcement live from block one, and a new executable harness for security patterns.

That is what a chain in final hardening looks like from the outside.

Which makes the signal to watch pleasantly falsifiable. The starting gun is a single object
appearing on `atomone-1`: the first `10-gno` client instance on mainnet, pointing at whatever
chain ID gno.land's mainnet claims. Today that query returns nothing. The day it returns
something, AtomOne has a cryptographic view of a live virtual machine for the first time, and
every claim in this piece stops being architectural and starts being operational.

After that the sequence is mechanical. Packets start moving on mainnet the way they have moved on
testnet for months. ATONE and PHOTON acquire IBC denominations on gno.land. Contracts that
already exist begin holding assets that already exist. None of it requires new invention, because
the invention already happened and was audited on a testnet.

There is one more thing that would have to be true for the full design to arrive, though, and it
is the piece that has not been built.

## The part that has not been built

An IBC bridge lets two chains trade with each other. It does not make one a shard of the other.

Everything described so far is a peer relationship. Two sovereign chains, each running its own
validators, each independently verifying the other's claims, connected by a wire that neither
controls. That is a genuine achievement and it is not what AtomOne's roadmap describes. The
roadmap describes gno.land as a consumer chain, which means something much stronger: gno.land's
blocks secured by AtomOne's validator set, rather than by its own.

The standard way to do that in Cosmos is Interchain Security, and AtomOne has ruled it out. Not
quietly, and not on technical grounds alone.

The argument comes from Jae Kwon, whose company builds both chains, and it arrives in two parts.
The first is economic. Classic ICS treated validator labour as elastic. It assumed operators
asked to run a second chain would absorb the servers, the monitoring, the upgrades and the
on-call burden in exchange for a share of a consumer chain's inflation, which might be worth
something or might not. The prediction was dozens of consumer chains. The outcome, by his own
accounting, is two.

The second part is a security objection, and it targets the fix the Cosmos Hub adopted. Partial
Set Security lets validators choose which consumer chains to secure, which sounds like the
obvious remedy to the first problem. Kwon's argument is that it introduces a worse one: a subset
of validators can opt into the same set of chains with the intent of attacking all of them at
once, and can deliberately avoid chains that would attract honest operators. His conclusion is
blunt, that "permissionless fractional staking is in general insecure."

Which produces an unusual design principle. The thing to make permissionless is not who
validates, but what gets deployed. As he puts it, what is needed is "not permissionless validator
participation (we need the opposite), but rather permissionless application chain deployment."

The replacement is called Validation-as-a-Service, and the analogy in the source is a cloud
provider: package a chain application into a container and upload it, so that "just like you can
simply deploy a linux container image to an Amazon EC2 server, you should be able to deploy one
to Gaia and AtomOne." Every shard secured by the root chain's full validator set. Its central
economic move is to stop paying validators in hope.

Under VaaS, a consumer chain pays a fixed fee, denominated in PHOTON, for every block it
produces. That amount is a parameter, it is distributed evenly across validators, and it does not
fluctuate with the consumer chain's token price or inflation schedule. Validators know what
running the chain pays before they agree to run it. And if the consumer chain's funding runs out,
the provider chain instructs it to halt.

That last clause is the part worth sitting with, because it is unusually honest for this
industry. Security becomes a metered utility with a shutoff valve. A chain that cannot pay for
its validators stops producing blocks, in the same way an unpaid server gets switched off. No
subsidy, no expectation that operators will eat the cost for ecosystem alignment, no ambiguity
about what happens when the money runs out.

It also does something specific to PHOTON. Its only job today is paying for AtomOne's own gas,
obtainable only by burning ATONE in a one-way conversion. One way that changes is the one
described above, PHOTON becoming an asset inside a virtual machine. VaaS is the other, and the
intended scope is broader than a fee token for one chain. The design has PHOTON serving as "the
exclusive fee-token for transactions across all shards, IBC fees, and ICS/VaaS payments, while
ATONE powers governance and staking." Every shard block becomes a PHOTON payment, and stakers
earn fees not just from the hub but from everything hosted on it. That is a materially different
asset from the one that exists now.

None of which has been built.

What exists is a prototype. `ics-poc-1` is real, merged code: a multiplexer that sits under a
single CometBFT consensus layer and routes transactions to multiple independent ABCI applications
by chain ID, with commits for dynamic consumer addition and incentive payment. It matches the
architecture the roadmap describes, containerized consumer shards where the consumer supplies
application logic and the provider supplies consensus and networking. It is a genuine engineering
artifact rather than a diagram.

It has had no commits since January.

The other thing that exists is preparation on the far side. Gno.land merged an architectural
decision record that removes two chain-side restrictions on validator set updates, and the stated
reason is explicit: those restrictions would prevent future AtomOne Interchain Security
provider-validator-set mirroring from applying updates to gno.land when AtomOne rotates its own
validators. Gno.land has already cleared a path for a mechanism that does not exist yet, in
shipped code, which tells you the plan is real inside both teams even though the implementation
is not.

And AtomOne's own roadmap is candid about the status. The phase containing validator incentives
and the improved ICS model is marked as not started.

So the accurate summary is narrow. There is a diagnosis of why the existing approach failed,
which is credible and comes from someone with standing to make it. There is a proposed
replacement with a clear economic mechanism and a named target release. There is a working
prototype of the execution architecture, currently dormant. There is preparatory code merged on
the consumer side. There is no implementation, no timeline anyone has committed to publicly, and
no way to test the model until there is.

That is a real gap, and it is worth naming rather than folding into the optimism, because the
rest of this piece is unusually well evidenced and this part is not.

But it is also the right gap to have. The hard part of connecting two chains is proving they can
verify each other without anyone being trusted, and that part is finished, audited, and has been
running across four chain resets. What is missing is an economic layer: who pays, how much, and
what happens when they stop. That is a genuinely difficult design problem, and Kwon's critique
suggests they understand exactly why the last attempt failed. It is also, unlike consensus
verification, the kind of problem you can iterate on in public without putting anyone's funds at
risk.

Cryptography first, economics second, is a defensible order to build in.

## What comes next

Most Layer 1s answer "what can you build here" by pointing at their own virtual machine. AtomOne
answers by pointing at a different chain, and the longer you sit with that decision the better it
looks.

A virtual machine is the largest attack surface a blockchain can voluntarily adopt. It is where
the reentrancy bugs live, the upgradeable-proxy disasters, the oracle manipulations, the
unbounded loops that halt block production. Every chain that hosts one has accepted that a
stranger's bad code is now, in some sense, its problem. AtomOne declined that trade, and in
exchange gets to be a chain whose failure modes are confined to things it wrote itself.

Gno.land took the opposite specialization and pushed just as hard. Contracts in Go, published as
source rather than bytecode, on a VM built for the purpose. Neither chain has to be adequate at
the other's job, and that is the actual elegance here. Two teams inside one company, each
building the thing they are best at, connected well enough that the split costs the user nothing.

The part that makes this credible rather than merely tidy is that the boring work is finished.
Somebody sat down and wrote a light client for a consensus engine that exactly one chain runs,
because no other way existed. Somebody paid for an audit that found a way to freeze it with a
duplicate message, and fixed that before a single token was at risk. Thirty-three packets have
crossed, four testnets have been destroyed and rebuilt underneath the thing, and every outbound
packet completed its round trip. That is not a demo. That is infrastructure, built quietly, in
the order a careful engineer would build it.

The pleasure of following this is how little of it asks for trust. Read the module list and see
there is no VM. Read the `allowed_clients` parameter and see gno.land was permitted in July.
Query the acknowledgements on `10-gno-16` and count the confirmations yourself. Check
`ics-poc-1`'s commit history and see for yourself that shared security is still a design rather
than a mechanism. Every claim here is one HTTP request from being confirmed or knocked down,
which is a rarer property in this industry than it should be.

And the next milestone is a single object appearing where there is currently nothing. Today
`atomone-1` holds zero `10-gno` clients, because there is no gno.land mainnet to point one at.
When that query returns a client, the architecture stops being a plan. Packets take the path they
have already worn smooth on testnet. ATONE and PHOTON acquire denominations inside a virtual
machine and become assets a program can hold rather than tokens that can only be staked or spent
on gas. The contracts are already written. The wire is already tested. The only missing piece is
the chain on the far end.

Which brings it back to that opening list, the one with the hole in it.

The hole was never a hole. AtomOne knew exactly what it was leaving out, and knew where it was
putting it instead. When that chain lands, a hub that deliberately cannot run a single line of
user code is going to get access to all of it, and will not have compromised one thing about
itself to do so.

That is a good bet, carefully made, and it is about to be tested in public.
