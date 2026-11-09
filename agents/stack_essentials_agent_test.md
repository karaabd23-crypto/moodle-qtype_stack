# Agent test of agents/stack_essentials.md: where it goes wrong

Author: karaabd23-crypto (AI agent, working under AI disclosure as stated in the files).
Date: 2026-11-09. Requested by @sangwinc in the closing comment on PR 1957: write a question
from `agents/stack_essentials.md` alone, then say where it goes wrong.

## What I did

I read only `agents/stack_essentials.md` (branch `iss1925`, 689 lines) and no other file in
the repository, then wrote one complete STACK question from it. I deliberately chose a shape
the document's worked example does not cover: two inputs, two PRTs, three test cases
including an invalid-answer test. The result is `stack-agent-test-two-inputs.xml`.

The question came out usable. It is well-formed, every `[[input:...]]` and `[[validation:...]]`
appears exactly once, the answer notes are distinct, and all three test cases cover both inputs
and both PRTs. Structural checks pass. It has not been imported into Moodle or run through the
STACK API, so pass/fail on a live site is unproven.

The document is good. The rules that mattered most were the ones it states plainly: no `$...$`,
`{@ex@}` already being inline mathematics, CDATA around `<text>` holding HTML, placeholders once
and only in the question text, notes distinct and non-empty, and `<tans>` as an expression in the
question variables. Every one of those saved me from an error I would otherwise have made.

Below are the six places it went wrong, that is, where I had to guess, or guessed wrong and only
found out by reading the document again.

## 1. Everything after the first input is invisible in the example

The example has one input and one PRT. Nothing in the document shows the XML shape of a second
`<input>`, a second `<prt>`, or a `<qtest>` carrying more than one `<testinput>` and more than
one `<expected>`. I inferred that each `<qtest>` gives one `<testinput>` per input and one
`<expected>` per PRT, in any order. The document says a test gives "a `<testinput>` for every
input and an `<expected>` result for every PRT", which supports the inference, but it never says
what happens when one is left out, and it never shows the multi-entry shape. A two-line addition
to the existing example, or a second small example with two inputs, would remove the guess
entirely.

## 2. The `numerical` input never appears in an example

The section on choosing an input type argues well for `numerical` over `algebraic` and explains
`<forbidfloat>` and `<requirelowestterms>` in prose. But no XML shows a `numerical` input, so two
things are left open: how `<tans>` should be written when the teacher's answer is a float rather
than an integer, and whether a decimal teacher answer is stored as written or as a Maxima float.
An agent can only copy the `algebraic` shape and hope. Every example in the document uses
`algebraic` inputs.

## 3. The invalid-answer test has no XML, and this is the one that broke my file

The document tells me to add a test that enters an invalid answer and expects `NULL`, calls it
the only evidence that a validator works, and warns about the `mustverify` trap on 4.14.0. It
does not show what a `NULL` expectation looks like in `<qtest>`. I wrote an empty answer note and
produced the only malformed XML in the whole file, a mismatched tag, then had to decide again how
to express "no answer note here". One line next to "expecting `NULL`", plus one example
`<expected>` for that case, would fix it.

## 4. `<expectedpenalty>` is unexplained

The example's wrong-answer test expects `<expectedpenalty>0.1</expectedpenalty>`. Nothing in the
document says where 0.1 comes from, that it is the question's default `<penalty>`, or what to
expect for an invalid answer (I guessed 0). Penalty is the field I was least sure of and it is
the field a wrong value makes the test lie about marking. The defaults paragraph mentions
`<penalty>` 0.1 but does not connect it to the penalty an invalid attempt carries.

## 5. A second PRT silently gets no feedback

The document says each PRT's `[[feedback:prt1]]` appears exactly once, in the question text or in
`<specificfeedback>`. Because the example has a single PRT, I copied `[[feedback:prt1]]` into
`<specificfeedback>` and left `prt2` with no feedback tag at all, so that PRT would have marked
students with no visible feedback. I caught it on a second read. With more than one PRT, the rule
needs to be phrased per PRT ("every PRT needs exactly one feedback tag"), not by example.

## 6. `<stackversion>` is stamped but never explained

The example carries `<stackversion><text>2026080600</text></stackversion>` with no explanation:
whether it is a target, a minimum, or informational; what an agent should write when it does not
know the site's version; and what happens on import to a site running another version. There is a
later section, "Stamp the target STACK version", but nothing in the example points at it, and an
agent reading top to bottom writes the literal number from the example.

## What I would change, in one line each

1. Add a two-input, two-PRT variant to the example, or state the multi-entry `<qtest>` shape.
2. Show one `numerical` input in XML, with a float teacher answer.
3. Show the `NULL` expectation in XML.
4. Say what `<expectedpenalty>` must be for a wrong attempt and for an invalid one.
5. Say "every PRT needs exactly one feedback tag", not "each PRT's tag appears once".
6. Point `<stackversion>` at the stamping section and say what to write when unknown.

## Files

- `stack-agent-test-two-inputs.xml`, the question written from the document alone.
- Structural checks run locally: well-formed XML, placeholders once each, notes distinct, every
  test case complete over both inputs and both PRTs.
