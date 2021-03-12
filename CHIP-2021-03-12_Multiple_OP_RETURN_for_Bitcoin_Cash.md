# CHIP-2021-03-12 Multiple OP_RETURNs for Bitcoin Cash

        Title: Multiple OP_RETURNs for Bitcoin Cash
        First Submission Date: 2021-03-12
        Authors: Benjamin Scherrey.
                 Earlier concept - [Jonathan Silverblood](https://bitcoincashresearch.orgt/2021-bch-upgrade-items-brainstorm/130/23) .
        Type: Technical
        Status: DRAFT
        Last Edit Date: 2021-03-12

## Summary

We propose to make presence of multiple OP_RETURN outputs qualify as standard transactions subject to the existing 223 byte limit for OP_RETURNs across all outputs of the transaction. We are silent in this proposal as to whether or not the [223 byte limit](https://github.com/bitcoincashorg/bitcoincash.org/blob/1b2008d3ac2b30a2ab26193dc8d3652fc33fb798/spec/may-2018-hardfork.md) is the correct one for BCH but are content to leave that alone in order to improve the chances of getting consensus for this change for the May 2021 fork. This makes the impact on nodes and miners as minimal and risk-free as possible while allowing developers to use OP_RETURN in a manner that justifies its core benefit - experimentation of new features/protocol updates without breaking existing systems or stressing the block chain with extraneous UTXOs which is an undesirable alternative option should this improvement not be accepted.

## Motivation and Benefits

Quite early in Bitcoin history, attempts to inject miscellaneous data into the block chain were made that made correct parsing and identification of transactions more difficult but, most importantly, bloated the UTXOs with superfluous data that potentially had a very real negative impact on operators of nodes, miners, and wallet users. The introduction of OP_RETURN with a restricted size limit that was deemed unspendable by the protocol was the compromise that protected everyone's interests. Allowing this data to be inserted but then also ignored by all who didn't care about it gave BCH developers an excellent tool in which to try out development ideas and protocol improvements in an isolated manner without introducing backwards or forward incompatibilities. Perhaps more importantly, it gave the entire BCH community an experimental model that allowed a natural maturity for protocol proposals to mature informed by actual use on the block chain rather than depend entirely on theoretical opinions on how such a protocol change might impact the BCH chain. SLP is an excellent example of such a circumstance where, after considerable efforts and real-life trials have been made, has resulted in serious proposals such as OP_GROUP to be able to be realistically considered for incorporation into the core protocol with significantly less risk than would otherwise have been possible. 

The primary limitation of OP_RETURN is that the protocol standard only allows for one instance of an OP_RETURN output per transaction which means developers who want to build on top of other OP_RETURN utilizing capabilities must either create complex parsing scripts or divide their efforts across multiple transactions. Both of these options introduce incredible complexity and often are simply not viable for demonstrating a real-world use case for how the feature would behave on the BCH chain should it be incorporated into the core protocol. 

This proposal aims to correct this minor oversight in order to bring the full potential of OP_RETURN's experimental expressiveness to the BCH chain without incurring any additional trade-offs that were part of its original design. 

## Specification

The only technical elements we've identified are changes to the existing node systems to remove the limit of a single OP_RETURN and to enforce the existing size limit for OP_RETURN across all aggregate OP_RETURNs in the transaction outputs. 

## Current Implementations

We have implemented these modifications in a local implementation of Bitcoin Unlimited's C++ source code and have successfully implemented a protocol that co-exists with SLP Non-fungible and Fungible tokens with little effort or complexity. See [Proposal Specification for Address-based Bitcoin Cash Auction System](https://github.com/ActorForth/Auction-Protocol/blob/main/proposal-spec.md) for details of this project.

## Implementation Costs and Risks

Evaluating the changes required across the popular nodes demonstrates that the impact in terms of lines of code affected are minor and isolated. For Bitcoin Unlimited there's an existing check that only one OP_RETURN exists which can be removed. A mechanism for counting the aggregate size of all the OP_RETURNs is quite simple to introduce in the same source file. Unit tests would be approximately twice the implementation size which we find typical of well designed C++ code. 

APIs that want to support multiple OP_RETURN are also easy to modify. 

## Ongoing Costs and Risks

After the initial changes are made to the core node systems no ongoing costs are anticipated. Risks are restricted to other non-standard experimental development code that may depend/demand that there only be a single OP_RETURN but, unless the code serves some purpose such as a linter or tx compliance tool, it's unlikely that such code was appropriate in the first place.

Presently the [SLP specification](https://github.com/simpleledger/slp-specifications/blob/master/slp-token-type-1.md#consensus-rules) requires its OP_RETURN to be at vout[0]. Any protocol code seeking to co-exist with SLP would want to ensure that it be in a later output to appear in the transaction in order to prevent accidental conflicts. We recommend that OP_RETURN based protocols no longer specify a positional requirement for their data but, instead, establish another mechanism for identifying which OP_RETURN belongs to them. 

Now that the limit of a single OP_RETURN has been removed, future well behaved development utilizing OP_RETURN would no longer be designed with such a presumption in mind.

### Design & Implementation Process Timeline

2020-09-21 [Initial comment on bitcoincashresearch.org](https://bitcoincashresearch.org/t/2021-bch-upgrade-items-brainstorm/130/23).
2020-10-14 [Trial implementation of proposal on Bitcoin Unlimited node](https://github.com/ActorForth/BitcoinUnlimited/commit/fd10cbd9872b157d906a03d3a4ccf7c0ddd42c65). (note: this doesn't implement aggregate size limit)
2020-10-?? Trial modifications to a [test fork of the Javascript bch-js API](https://github.com/ActorForth/bch-js) and the python Bitcash API libraries (currently in a private repo) completed.
2021-02-24 [Proposal to be introduced for the May 2021 fork](https://bitcoincashresearch.org/t/multiple-op-returns-this-time-for-real/315).
2021-03-21 This CHIP created.
2021-03-24 Proposed completion for PRs to public Bitcoin Unlimited, Bitcoin Cash Node, bch-js API, and Bitcash API projects to support this CHIP. Basically T+9 days after proposal is approved.
2021-05-?? Deployment to BCH Mainnet Chain

## Evaluation of Alternatives

Alternatives to this proposal amount to restating original alternatives to OP_RETURN (such as the multi-sig data hack), introducing complex parsing requirements to OP_RETURN outputs, or dividing OP_RETURN dependent submissions across multiple transactions. All are vastly more complex and risky than this proposal. 

## List of Major Stakeholders


## Statements

### Full node developers

### Major businesses

### Miners and pools

### Application Developers

### Service Operators

## Discussions

[CHIP Discussion at Bitcoincashresearch.org](https://bitcoincashresearch.org/t/multiple-op-returns-this-time-for-real/315)
[Issues/PRs for this CHIP](https://github.com/ActorForth/Auction-Protocol/issues)
Our team is available for chat on the [ActorForth gitter](https://gitter.im/ActorForth/community).

## Copyright

BSD 3-Clause License

Copyright (c) 2018, 2020 Benjamin Scherrey, BiggestFan Productions Co. Ltd
All rights reserved.

Redistribution and use in source and binary forms, with or without
modification, are permitted provided that the following conditions are met:

1. Redistributions of source code must retain the above copyright notice, this
   list of conditions and the following disclaimer.

2. Redistributions in binary form must reproduce the above copyright notice,
   this list of conditions and the following disclaimer in the documentation
   and/or other materials provided with the distribution.

3. Neither the name of the copyright holder nor the names of its
   contributors may be used to endorse or promote products derived from
   this software without specific prior written permission.

THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS"
AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE
IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE
DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE
FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL
DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR
SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER
CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY,
OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE
OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
