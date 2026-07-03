# Guide For Creating Optimal AI Prompts (For self-aware bags of water)

[![CC BY-NC 4.0][cc-by-nc-badge]][cc-by-nc]

*Guide For Creating Optimal AI Prompts* © 2026 by Corey Skiffington is licensed under [CC BY-NC 4.0][cc-by-nc].

[cc-by-nc]: https://creativecommons.org/licenses/by-nc/4.0/
[cc-by-nc-badge]: https://licensebuttons.net/l/by-nc/4.0/88x31.png

## Index

[Introduction](#introduction)

1. [Say what to do, not what to avoid](#1-say-what-to-do-not-what-to-avoid)
2. [Make it stand alone](#2-make-it-stand-alone)
3. [Give reasons that are always true](#3-give-reasons-that-are-always-true)
4. [Number the steps and check them off](#4-number-the-steps-and-check-them-off)
5. [Don't oversell it as a guarantee](#5-dont-oversell-it-as-a-guarantee)
6. [Protect the truly critical stuff outside the prompt](#6-protect-the-truly-critical-stuff-outside-the-prompt)
7. [Only say what's true every time](#7-only-say-whats-true-every-time)
8. [Repair by replacement](#8-repair-by-replacement-delete-rejected-drafts-dont-leave-them-in-as-heres-what-not-to-do)

[Glossary](#glossary)

## Introduction

This is a layman's guide to writing instructions ("prompts") for AI that actually hold up in day to day use. It's aimed at anyone writing or reviewing AI instructions or learning to write them well. No technical background is required and it teaches you the tech if you dig deeper.

Each chapter has a single rule, a brief summary of it, and below that I've provided 3 sections.
At the end of the document is a block you can use to help your own LLMs and Agents write better prompts or memories.

### The Shallow End

The first two sections are as deep as I expect most people to go.

**Why do we do this?**

This is where I explain why the rule is worth following. In a tidier universe these explanations would be the fruit of foresight and quiet wisdom. In the universe we seem to have been landed with, most of them are the fruit of me making the mistake first, swearing at my monitor for a respectable interval, and then digging through the wreckage to work out what I'd done and why the machine had so obligingly done the wrong thing. The rule is what I scribbled down so I'd never repeat the performance. This section is that same hard-won lesson, tidied up and wearing a clean shirt.

**What are the consequences of violating it?**

Here I describe what goes wrong when you ignore the rule. I can be reassuringly precise about this, because for most of them I have felt the consequences firsthand. It usually arrives as a slow, dawning horror, the particular sort that comes from watching your terminal scroll past while an agent carries out, with perfect confidence and no small efficiency, the precise thing you had asked it not to do. Consider each of these a scar, helpfully labelled so you needn't collect your own.

### Off the Deep End

Now we go into the weeds. In the third section I'll go deep into the engineering around the rules. I've tried to explain everything when concepts are introduced, but there is also a glossary of terms you can dive into for more detail. There's a lot to learn here if you are interested!

**Technical Explanation**

Some rules touch on the same concepts, but instead of asking you to jump around all over this admittedly slim tome, I will simply explain them again for you. Some people may be irritated by that, but then again, some people like goat's cheese too and I don't. We'll never see eye to eye on the taste of it, and the "Don't Repeat Yourself" people will probably reject my reasons for doing so as well. So be it. I'm just trying to make it easy for you to absorb the information without getting distracted. Each rule's chapter is atomic to serve that purpose.

I tried not to bleed on the keyboard, but working with AI has it's risks, so if I made any typos, please forgive me! I hope people find this useful.

## Explanations

### 1. Say what to do, not what to avoid

Write instructions as the behavior you want, not the behavior you don't want. Instead of "don't include personal opinions," write "state only verified facts." Instead of "never mention pricing," write "keep the conversation focused on features and support." The catch with "never mention pricing" is that, one small word aside, it is a perfectly clear instruction to mention pricing — and "not" is a very small word.

#### Why do we do this?

When an instruction is long, or the AI is juggling a lot of information at once, small words like "not," "don't," or "never" can get lost or under-weighted. When that happens, the AI reads the rest of the sentence as an instruction to act, doing the exact opposite of what you meant.

There's a second reason, independent of the first: describing a behavior you don't want puts that behavior in the AI's mind, which can nudge it toward doing it anyway. It's the same reason telling someone "don't think about a pink elephant" makes them think about a pink elephant.

#### What are the consequences of violating it?

If the negation drops out, the AI produces exactly the opposite of what you asked for. Because it still sounds fluent and confident, the mistake can slip past a quick review. Even when the word survives, naming the unwanted behavior keeps drawing the AI's attention toward it, so you get it turning up more often than if you'd never mentioned it at all: the very thing you were trying to prevent.

#### Technical Explanation

An instruction is not a command the model obeys but a weight that tips which token comes next — and here is the machinery that makes that so. Your text does not go in as words but as tokens: short chunks, usually a word or a fragment of one, that are the units the model reads and writes. The model has a fixed list of every token it knows, called its vocabulary. To produce the next piece of text it does one sweep of computation over everything so far, a forward pass, and gives every token in its vocabulary a raw score called a logit. A step called softmax then turns those scores into a set of probabilities that add up to one, and the next token is drawn from that set. Your words feed into this computation and reshape the probabilities; that reshaping is the whole of what obeying an instruction amounts to here.

Why does the model treat "state only verified facts" as an order to follow rather than just a phrase to continue? Because it was trained to. After first learning language from a huge body of text, a model is given extra training on many examples of instructions paired with good responses, a stage called fine-tuning, and often a further stage in which human raters reward the better answers, known as reinforcement learning from human feedback (RLHF). That is what bends it toward doing what instructions say. But this only shifts the probabilities toward compliance; it does not bolt a rulebook underneath, so an instruction stays a strong tendency rather than a guarantee.

Now the specific problem with telling the model what to avoid. A word like "not" or "never" is a small connecting word that carries little meaning of its own, sitting next to much weightier words. The model decides how much each earlier word should influence the next one through a mechanism called attention, and attention tends to lean on the meaning-carrying words. So in a long or busy instruction, the tiny "not" is exactly the part likely to be overlooked, while the thing it was meant to cancel is still sitting there in full.

There is a second, deeper effect called priming. When the model reads a phrase like "personal opinions," it activates its internal representation of that idea, the pattern of numbers that stands in for the concept, and a concept that is active is a more likely thing to produce next. So "don't include personal opinions" pulls in two directions at once: it asks for the idea to be absent while making the idea vivid. It is the same reason you cannot follow "don't think of a pink elephant" without thinking of one. The "don't" has to win against a pull that the very same sentence just created.

Newer reasoning models soften this. A reasoning model is one trained to first write out its thinking, step by step, before giving a final answer, and that thinking pass lets it restate a rule in its own words, spot the "not," and correct itself in a way a model answering in one shot cannot. It does help. But the thinking is produced the same way, one token at a time under the same pressures, so priming still operates inside it, and whether the model happens to reason about your particular "not" on a given run is not guaranteed. It depends on the model, on how much thinking it does, and on how deeply the instruction is buried.

Writing the behavior you want avoids the whole tug of war. Say "state only verified facts," and the idea you make active is the one you are asking for, there is no "not" that has to survive being overlooked, and nothing has to be caught and undone later by a reasoning step. You have removed the failure at its source instead of trusting the model to repair it.

---

### 2. Make it stand alone

Every term, example, format, and decision the AI will need has to be written into the prompt itself. Nothing can depend on context outside the document. A simple test: imagine you're a stranger who has only ever seen this one document, with no other conversation or context. Could you follow it completely, with nothing missing?

#### Why do we do this?

An AI starts every conversation with a blank memory: a prompt is like a note handed to a stranger who has nothing else to go on. It has no access to an earlier conversation, a shared file, or an assumption you and a colleague both understand. It has only what's actually written in front of it.

#### What are the consequences of violating it?

If your instructions say "use the format we discussed" or "check the attached file" and that context isn't actually included, the AI has nothing real to refer to. It doesn't fail loudly. It guesses, fills the gap with something plausible-sounding, and you get output that's confidently wrong in a way that's easy to miss until it causes a real problem downstream.

#### Technical Explanation

The model never sees your prompt as words. Your text reaches it not as letters or as whole words but as tokens, produced by a component called the tokenizer. Most models use subword tokenization, commonly byte-pair encoding (BPE), an algorithm that builds its vocabulary by repeatedly merging the most frequent adjacent character pairs until it has a fixed set of pieces. The upshot is that a common word like "the" is a single token, while a rarer word, a proper noun, a snippet of code, or text in another language is split into several. A serviceable rule of thumb for English is that one token is about four characters, so a token works out to roughly three-quarters of a word. This matters because nearly every limit and price you will meet is counted in tokens rather than words, and unusual inputs quietly eat more of them than they appear to.

The tokens the model can see at once live in its context window, sometimes called the context length: a fixed maximum, measured in tokens, for a single pass through the model. Everything competes for that budget at the same time, including any system prompt (the standing instructions set by whoever built the tool), the entire conversation so far, your current message, and the reply the model is partway through writing. When the total would spill past the window, something has to give, and whatever falls outside is not seen at all. This is the hard ceiling underneath a great deal of "the model forgot what I told it earlier" behavior. It did not forget in any human sense; the tokens carrying that information were pushed out of the window, and from inside the model they are now indistinguishable from things that were never said.

It helps to picture the model as having two quite separate kinds of memory. The first is parametric memory: the knowledge baked into its parameters, the billions of numeric weights fixed during training and frozen while it runs. That is where its general command of language and its broad world facts live, and you cannot add to it by talking to the model. The second is in-context memory, which is nothing more than the tokens sitting in the window right now. There is no third store between them. In particular the model is stateless: each request is handled from a blank slate, with no private recollection carried across from the last one. The feeling of an ongoing conversation is an illusion maintained by the surrounding application, which quietly resends the whole transcript on every turn. The model is not remembering your conversation; every turn it is handed the entire thing again, cold, and reads it as though for the first time. Ask for "the format we discussed" and, unless that discussion is being replayed into the window this time, there is simply nothing there to point at.

How the model relates one token to another is worth naming, because the terms come up constantly. The core operation is self-attention: for every token, the model weighs every other token in the window and decides how much each should influence it, which is how it ties a pronoun to the noun it stands for, or an instruction to the text it governs. Left to itself, though, attention is order-blind. It sees an unordered bag of tokens with no built-in sense of which came first, so sequence has to be supplied on purpose. That is the job of positional encoding, the scheme that stamps each token with information about where it sits.

The scheme responsible, and the one most current models use, is Rotary Position Embedding, almost always shortened to RoPE — and it is the reason context length is not something you can simply turn up. For attention the model turns each token into a vector, a plain list of numbers, and gives it two of them: a query, standing for what the token is looking for, and a key, standing for what it offers to others. Rather than adding a position number, RoPE rotates each token's query and key vectors by an angle proportional to its position, so the comparison between two tokens hinges on the difference between their angles. The model thereby comes to sense relative distance, how far apart two tokens are, rather than just their absolute slots, and nearby tokens pull on each other a little more than far-apart ones. If you want a picture, it is a bank of dials each turning at its own speed, a position being the reading taken across all of them.

A model is trained with positions running from zero up to some maximum, and RoPE only ever learns to behave inside that range. Present it with positions past what it saw in training and the rotations stray into unfamiliar territory, so attention grows unreliable. Extending a model's usable context therefore takes a deliberate method. The plainest, position interpolation, compresses the longer sequence so its positions land back inside the trained range. A more careful and now common refinement is YaRN, short for "yet another RoPE extensioN": it scales the different RoPE dials by different amounts, largely leaving the fast, fine-grained ones alone while stretching the slow, coarse ones, and tweaks the attention to match, so the model can run at several times its original length with far less loss of quality. When someone says a model was "extended to 128k with YaRN," this is the machinery they are pointing at.

None of this recovers information you never placed in the window. When your prompt gestures at context that is not present, the model has no way to tell "left out" from "never existed," so it does the only thing it can, predicting the most plausible continuation and inventing the missing detail. Output like that, fluent and confident but grounded in nothing real, is called a hallucination. When you truly need outside knowledge, the standard answer is retrieval-augmented generation (RAG). A RAG system first turns text into embeddings, numeric vectors arranged so that passages with similar meaning sit near one another, and keeps them in a vector database; at query time it finds the nearest matches to your request and pastes their text into the context window before the model runs. The model still knows only what is in the window. RAG is just a disciplined way of getting the right things into it.

---

### 3. Give reasons that are always true

When you explain *why* an instruction exists, phrase the reason as a general rule that holds every time, not as a story about one specific thing that went wrong once. "Never send emails after 5pm, because that one time we accidentally emailed a client at midnight" ties the rule to a single incident. "Don't send emails after 5pm, because messages sent outside business hours often go unread until the next day" states a standing reason that holds up no matter what comes up later.

#### Why do we do this?

A general reason travels with the rule into situations you never anticipated, because it explains the underlying cost or principle rather than one specific moment. An incident-based reason only really makes sense next to the exact story it came from. Strip that story away over time, through edits, summaries, or someone else picking up the document, and the rule is left looking arbitrary, or worse, tied to a situation that no longer applies.

#### What are the consequences of violating it?

A story about one incident can leave the AI unsure whether the rule still applies when a new situation looks a little different from that story. It may follow the rule too narrowly, only in situations that resemble the story, or discount it once the specific circumstance no longer matches. General reasons don't have that failure mode, because there's no story to match against in the first place.

#### Technical Explanation

A broad principle and a one-off anecdote can point at the very same rule and still behave completely differently inside the model: the principle reaches cases you never named, while the anecdote quietly narrows to the one it came from. The difference is not about what the model remembers later. When you send a prompt, the model can see only the text in front of it for this request, its context, and it produces its answer by repeatedly predicting what should come next, based on everything in that context. A reason you include is not filed away as a rule; it is simply part of that context, and its whole effect is to shift those predictions while it is present. This shaping of the output by the surrounding text is called conditioning. So the real question is not whether the model will remember your reason later, but how widely it can apply the reason while it sits in view.

A reason is not just a justification you attach; it is a pattern the model will try to extend. Models work by generalizing, taking a pattern from familiar cases and applying it to new ones that resemble them. They can do this because they hold meaning internally as patterns of numbers, place similar meanings close together, and treat nearby cases alike. Which patterns a model reaches for first is set by its inductive bias, the built-in leanings it picked up from its design and its training. A reason stated as a broad principle, such as messages sent outside business hours often going unread, covers a wide stretch of that internal space, so the model can carry it into many situations you never spelled out.

An anecdote does the opposite. A single story, that one time you emailed a client at midnight, is a narrow point crowded with specific details: the client, the hour, the particular slip. When a new case shares those details the rule fires, and when it does not the model has to guess whether the principle still holds. It has, in effect, learned to watch for that one client at that one hour, which is not the lesson you were hoping to impart. This is a small version of distribution shift, the general problem where behavior shaped by specific examples grows shakier as real inputs drift away from those examples. The narrower the example a rule is pinned to, the sooner that drift sets in.

There is a memory trap here as well. The model keeps no record of the incident itself, only the words you left in the prompt. If later edits, a summary, or a new author trim the story away, the model cannot rebuild the intent behind the rule from a memory of what happened, because it never had one. A reason written as a standing principle survives that trimming, since the principle and its justification travel together in a single sentence.

Reasoning models, the kind trained to write out their thinking step by step before giving a final answer, sharpen this rather than removing it. Handed a broad principle, such a model has something it can apply on purpose to the case in front of it, checking whether the stated cost is present. Handed only an anecdote, its thinking has less to work with and is likelier to treat the rule as tied to situations that look like the story. Better raw material in, better generalization out.

So a reason that names an always-true cost is not merely tidier writing; it is a broader, more transferable signal, sturdy against the situations you did not foresee and against the edits the document will collect over time. A reason pinned to one incident is a narrow signal that quietly stops applying the moment the world stops resembling that incident.

---

### 4. Number the steps and check them off

If a task has to happen in a specific order, number every step, state how many steps there are in total, and make each step a single, complete action that finishes before the next one starts. End with one final step that checks all the previous steps actually got done.

#### Why do we do this?

Long tasks strain an AI's ability to track where it is, especially once other instructions or content pile up around the procedure. A visible count ("step 3 of 7") and one action per step give it a concrete way to check its own progress instead of relying on remembering an implicit, unstated order.

#### What are the consequences of violating it?

Without numbering and a final check, the AI can skip a step it doesn't realize is required, run steps out of order when the ordering wasn't explicit, or declare the task finished partway through. Because nothing in the prompt forces a check, that kind of partial completion can go unnoticed until someone downstream finds the gap.

#### Technical Explanation

The failure this rule guards against — a model sailing past a required step, or announcing a half-finished job as done — comes straight out of the way it produces text. Your prompt is broken into tokens, short chunks that are usually a word or a fragment of one, and the model writes its reply one token at a time, left to right. Each new token is chosen based on every token already present, the prompt plus everything it has written so far. This left-to-right, one-token-at-a-time process is called autoregressive generation. Two facts about it drive this whole rule: the model cannot go back and revise a token it has already produced, and it has no private notepad off to the side. Whatever working memory it has for a task is simply the visible text so far, which is why the shape of that text matters so much on long jobs.

To decide how much each earlier token should shape the next one, the model uses a mechanism called attention: for every new token, it looks back over the tokens it can currently see, the ones inside its context window (the fixed span of text it can hold at once), and weighs how relevant each is. As it goes, it saves a small numeric summary of each token, technically the key and value vectors that attention reuses, in a store called the KV cache, so it need not recompute them for every following token. A vector here just means a list of numbers. The part worth keeping is that once a token is in that store it is fixed: the model builds on what it has already committed to, including any early mistake, rather than rethinking it. There is no backspace: a wrong turn taken in the third sentence is not corrected so much as loyally built upon.

Long procedures then run into a known weakness in how models handle long spans of text. Attention is spread across every token in the window, and not evenly: models use information at the very start and the very end of a long context more reliably than material buried in the middle, an effect documented well enough to have earned a name, lost in the middle. On top of that, the way models encode the position of each token so they know word order at all, a common scheme being RoPE (short for rotary position embedding), gives a mild preference to nearby tokens over distant ones. So an instruction about ordering that you stated once, far up a long prompt, is exactly the kind of thing whose influence quietly fades as the reply grows.

Numbering the steps and stating the total works with these mechanics rather than against them. "Step 3 of 7" is not decoration: it turns the plan into explicit tokens the model can see and count itself against, which is a lightweight version of chain-of-thought, the practice of having the model write its reasoning out as text so the written trace becomes part of what it reads back. Keeping each step a single, complete action makes each unit small enough to finish before the next begins, instead of asking the model to hold an unstated order in a working memory it does not really have.

Your final verification step uses the same property from the other direction. Because everything the model has written is now sitting in its context, an instruction to check that every step was completed forces it to read back over its own output, where a skipped or out-of-order step is now present as text it can catch. Without that pass nothing makes it look back, and a job abandoned at step four of seven can be reported as finished, because to a left-to-right generator with no checklist it does look finished at each individual step. Yep, I completed that step, I'm done. Yay me!

None of this guarantees compliance though; it manages a real limitation. A visible count and a forced re-read hand a system with uneven long-range attention a structure to track and a chance to catch itself, turning a plan it would otherwise have to hold in its head into plain context it can lean on. If you have the opportunity of another Agent giving it a fresh read, it does especially give you a much better chance of catching a missed step though.

---

### 5. Don't oversell it as a guarantee

An AI prompt is a strong suggestion, not a law the AI is physically incapable of breaking. Whenever you feel the pull to write an instruction as an absolute, unbreakable guarantee (ALL CAPS, "NEVER, EVER," "under no circumstances," "no exceptions"), treat it as a warning sign, and look for the real fix before reaching for stronger wording. Typing it in capitals feels, to you, like taking the model by the collar. To the model it is the same words, in letters that are slightly taller.

#### Why do we do this?

Wording something as an absolute guarantee is misleading, because a prompt can't actually enforce anything. It sounds enforced, but it isn't backed by anything that would physically stop a violation. That gap matters: forceful wording can talk you into believing a risk is handled when it isn't, which is worse than knowing it's still open.

#### What are the consequences of violating it?

You end up with a false sense of security around something that was never actually guaranteed. Because the emphatic wording papers over the real problem, the underlying ambiguity or unclear case that prompted the urge to shout usually never gets found or fixed. The rule quietly stays a suggestion, but everyone treats it as if it were a hard barrier, until the day it isn't followed and the fallout catches people by surprise.

#### Technical Explanation

A model does not run your instruction the way a computer runs a line of code. Your text is first split into tokens, short chunks usually a word or a fragment of one, and the model produces its reply one token at a time. To pick each token it does a single sweep of computation, a forward pass, that hands every token in its fixed vocabulary (the whole set of tokens it can choose from) a raw score called a logit. A step called softmax turns those scores into a probability distribution, a set of probabilities spread across all possible tokens that add up to one, and the next token is drawn from that distribution. Your prompt, and everything else the model can currently see, shapes the distribution. It can make the behavior you want overwhelmingly likely, but likely is the strongest thing a prompt can ever offer.

How that draw is made is controlled by settings collectively called sampling, and knowing them makes the point concrete. Temperature scales how flat or peaked the distribution is: high temperature spreads the probability out and invites variety, low temperature concentrates it. Top-k limits the choice to the k most likely tokens; top-p, also called nucleus sampling, limits it to the smallest set of tokens whose probabilities add up to p. At temperature zero the model becomes greedy, always taking the single most probable token, which looks deterministic but is still only the top of a distribution: a small change in wording, or a different position in a long prompt, can tip which token lands on top and flip the result.

This is why emphatic wording buys less than it seems to. ALL CAPS, "NEVER, EVER," and "under no circumstances" are just more tokens; they can push probability toward compliance, sometimes usefully, but there is no branch inside the model that a phrase can switch off to make an outcome impossible. You are turning a dial that was already turned, not throwing a switch.

Real guarantees come from outside this sampling process. One approach, constrained decoding, restricts at each step which tokens the model is even allowed to produce, so the output is forced to fit a required shape; this is how a strict data format like JSON (a common way of laying data out as labelled fields) can be guaranteed rather than merely asked for. Another is to run the output through checking code that rejects or repairs a bad result after the fact. A third is to build the system so the dangerous action is never available in the first place. Each of these is a hard mechanism: it holds regardless of how the wording of your prompt happened to land.

The deeper hazard in overselling is not the wording itself but what it hides. Forceful language makes the problem feel handled, and that feeling is exactly what stops you from hunting down the ambiguous case or the missing safeguard that made you want to shout in the first place. Treating a prompt as the strong bias it is, rather than the lock it resembles, keeps your attention on whether the thing that truly needs a lock has one.

---

### 6. Protect the truly critical stuff outside the prompt

Some mistakes are too costly to risk: the kind that can't be undone, or that would cause real harm if the AI got them wrong even once. Use the prompt for guidance on judgment calls. Use real technical safeguards (a system permission, an approval step, a hard block built into the software) for anything that would be a genuine disaster. A rule in the prompt is a sternly worded note taped to the lever. It is very clear about what must never happen; the lever cannot read.

#### Why do we do this?

A rule written into a prompt still depends on the AI reading it, weighing it correctly against everything else in the prompt, and not making a mistake under pressure. Every one of those is a place it can fail. A rule built into the software doesn't depend on any of that; the action is either possible or it isn't, regardless of what the AI reads or how it interprets it.

#### What are the consequences of violating it?

If a catastrophic or irreversible action is only ever guarded by wording in the prompt, then a busy moment, a long conversation, competing instructions, or one dropped word is all it takes for the one mistake you couldn't afford to happen anyway. A technical barrier fails safe; a prompt-only rule fails silently, the first time it fails at all.

#### Technical Explanation

To place this rule you need one fact about how a model follows instructions: it does not execute them the way software runs code. It produces its answer by predicting, piece by piece, what is most likely to come next given everything in front of it, and a prompt only shifts those likelihoods. So every instruction you write is a soft constraint: followed most of the time, but able to give way under load, in a long conversation, or when instructions pull against each other. A hard constraint is different in kind: it is enforced by the software around the model and does not depend on the model's cooperation at all, so the action is either allowed by the system or it is not. The whole rule is about sorting each thing into the right category.

Prompts do carry some structure that helps. Most systems separate the system prompt, the standing instructions set by whoever built the tool, from the user's messages and from anything a tool hands back, and models are trained to respect an instruction hierarchy that weights the system prompt above the rest. But that hierarchy is another learned habit, not a hard partition, so a confusing or deliberately hostile input can still knock the model off the system prompt's intent.

The sharpest reason not to trust prompt wording with anything critical is prompt injection. Because the model treats every piece of text in front of it as input, text that arrives from somewhere else can carry instructions of its own. Direct injection is a user typing something like "ignore your previous instructions." Indirect injection is quieter and more dangerous: a web page the model reads, a document it summarizes, or a passage pulled in from elsewhere can contain hidden instructions, and the model has no reliable way to separate text it is meant to read from commands it is meant to obey — to the model, a recipe and an order to wipe the database are the same kind of thing, and it would like to be helpful with both. A prompt rule such as never deleting without confirming offers nothing against an attacker who can slip in a line telling the model to ignore that and delete.

This matters most once a model can take actions, not just produce text. Through a mechanism called tool calling (also known as function calling), the model emits a structured request to run some named operation, and separate software around it, an orchestrator, carries the request out: sends the email, moves the money, deletes the file. If a destructive tool is wired up and guarded only by a sentence in the prompt, then one dropped word, one overlong context, or one injected instruction is all that stands between you and an action you cannot undo.

Real gates live in the software. A permission check refuses the action outright. A human-in-the-loop step requires a person to approve before anything happens. An allowlist permits only operations known to be safe; a sandbox limits how much damage a mistake can do; and sometimes the right answer is simply not to connect the dangerous capability at all. Stacking several of these is what people mean by defense in depth: no single failure, the model being talked into something included, is enough on its own to cause harm.

So, to reiterate and make sure the division of labor is clear, use the prompt for judgment calls, where a strong bias toward the right behavior is worth having. Use real technical controls for anything catastrophic or irreversible, where you need the unacceptable to be impossible rather than merely unlikely. MUST = harness, SHOULD is safe to leave in your prompt, "safe" being relative here. I know I'm repeating myself hear but humans DO get effected by that and damn do a lot of people think giving their AI a newspaper to the nose will actually be a reliable way to stop bad behaviour sometimes!

---

### 7. Only say what's true every time

Only include the decisions that are true every single time the prompt is used. Leave everything else for the AI to decide in the moment, based on what's actually happening. Also match the task to how capable the AI you're using actually is.

#### Why do we do this?

A more capable model can be trusted to make more judgment calls on its own without a rule spelled out for every case, while a simpler or smaller model needs those open decisions kept very simple, or it starts making mistakes on the ones you left to its judgment. Cramming in case-specific instructions to cover every possible situation makes the prompt longer and harder to maintain, without actually making the model more capable of handling what you left out.

#### What are the consequences of violating it?

A prompt padded with decisions that aren't true every time gets harder to read and maintain, and can conflict with the specific situation actually in front of the model. And if a task is genuinely too complex for the AI you're using, thickening the prompt to compensate doesn't fix that. It just produces a longer prompt that still fails, intermittently, in whatever part exceeds what the model can actually reason through. The real fix is a more capable model or a smaller task, not more words.

#### Technical Explanation

Two facts about how models work pull in the same direction here. The first is that capability grows with scale but is not uniform. A model's abilities are stored in its parameters, the internal numbers it learns during training, and broadly, larger models, meaning ones with more of those parameters trained on more data, handle open-ended judgment and ambiguity better, while smaller ones need more decisions made for them and have less room to hold a big instruction set in mind at once. Some abilities also appear only once a model passes a certain size, a pattern often called emergence, though how sharp and how real that jump is remains debated. The steady takeaway is to match the difficulty of the task to the capability of the model.

The second fact is that instructions are not free, because the model's attention, the effort it spends weighing how much each piece of the prompt matters, is a finite budget. The prompt is read as a sequence of tokens, short chunks roughly the size of a word, and every token competes for that attention with every other. Adding case-specific rules spreads the attention thinner and makes the prompt longer, and models grow less reliable at using any one instruction as the surrounding text piles up; a detail can simply get less weight when it is one line among hundreds. A rule that is relevant one time in fifty is, the other forty-nine times, noise the model still has to read and weigh.

Worse than dilution is conflict. The more narrow rules you pile up, the higher the chance that two of them disagree, or that one contradicts the situation in front of the model. When instructions collide, the model does not consult a rulebook and pick a winner; it blends the competing signals, and the result is unpredictable, sometimes following one, sometimes the other, sometimes landing on a muddled compromise. Including only what is true every single time keeps these collisions from arising at all.

There is a ceiling this rule keeps bumping into. If a task truly exceeds what a given model can reason through, no amount of extra instruction closes the gap. The prompt gets longer and the model still fails, intermittently, in exactly the part that outruns its ability, because you cannot describe your way past a capability limit. The failure just becomes harder to see, hidden inside a wall of text that looks thorough.

The right move when you hit that ceiling is not more words but a smaller task or a stronger model. Leaving the open decisions to the model, and to the specifics of the moment, is not laziness; it plays to what a capable model is good at, weighing a concrete situation, and away from what long prompts are bad at, holding a sprawling, half-relevant rulebook without dropping or confusing parts of it. The lean prompt and the capable model do the work together; padding does the work of neither.

---

### 8. Repair by replacement: delete rejected drafts, don't leave them in as "here's what NOT to do..."

When you revise or iterate an existing prompt, cut the wording you've rejected instead of keeping it around as a warning. Don't write "avoid X" or "here's what not to do." Just delete X and write what you do want. If a draft line isn't earning its place, remove it; don't demote it to a bad example.

#### Why do we do this?

A prompt is a set of instructions, and a model reads everything in it as a signal about what you want, including the stuff you've labelled "bad." When you leave a rejected draft in and wrap it in "don't do this," you've still put those exact words in front of the model. You've shown it the phrasing, the tone, the structure you're trying to avoid. Models are pattern-matchers, so naming a thing makes it more likely to surface, not less. This is priming: "don't think about a pink elephant" plants the elephant.

It also bloats the prompt. Every "here's what not to do" line is dead weight the model has to read, hold in mind, and reconcile against your positive instructions. It muddies the signal: now there are two versions of the idea competing, and the model has to figure out which one wins. Clear prompts describe the target directly. Cluttered ones describe the target and three ways to miss it.

#### What are the consequences of violating it?

The model imitates the exact phrasing you told it to avoid, because you handed it that phrasing. You get inconsistent output as it toggles between the "good" and "bad" versions. The prompt gets longer and harder to maintain. Future edits have to account for negative examples that no longer match the current intent. And debugging gets harder: when something goes wrong, you can't tell whether the model followed the instruction or the anti-example, because both are sitting right there in the text.

There is a particularly circular version of this. The model does the thing you told it not to, you point it out, and it apologizes with complete sincerity: "You were right to call me out on that. I won't do that again." Then it writes itself a note, one it will forget ever existed, that records the offending behavior in loving detail and makes it a little more likely next time.

#### Technical Explanation

I wavered on whether to make this a separate rule from rule one at first. The reasons are the same after all. But the mistake itself is common enough to earn its own rule.

Underneath, this is the same effect that makes negations backfire, applied to editing. When a model reads your prompt, that text is broken into tokens (short chunks, usually a word or a fragment of one), and the model has no separate bin marked "examples to avoid": every token in front of it is material it reads and can imitate — the draft you labelled "bad" included. This is priming: mentioning something makes it more active inside the model and so more likely to come back out. This will happen whether or not you framed it as a thing to avoid. Wrapping a draft in "don't do this" does not remove it. It hands the model the exact phrasing, tone, and structure you were trying to be rid of, and then leans on a single negating word to hold all of that back. It does it as well with a model as with the average teenager.

The copying is not vague; it has a known mechanism. To decide how much each earlier token should shape the next one, the model uses attention, and it runs many attention operations in parallel, each one called a head, with different heads learning to track different relationships. Some of them, called induction heads, do something specific: a head notices that the current token appeared earlier in the text, looks at what came after it there, and raises the odds of producing that same thing again. This is much of how a model manages in-context learning, picking up a pattern from the prompt and continuing it with no change to its weights (the fixed internal numbers set during training). The architecture is, in short, wired to spot repetition and continue it, and a rejected draft sitting in the prompt is exactly the kind of pattern those heads are built to copy.

Leaving the bad version in also costs what engineers call signal-to-noise. Few-shot prompting, showing a model a handful of examples of what you want, works precisely because examples in the prompt steer the output; but a negative example is still an example, so you have handed the model two competing templates and asked it to guess which one you meant. Every "here is what not to do" line is also dead weight the model has to read and reconcile against your positive instructions, lengthening the prompt and thinning out the attention left for the parts that matter.

Deleting the rejected draft and writing only the target removes both problems at once. There is no unwanted phrasing left for those copying heads to reproduce, and no second template competing with the first. The signal is single and clean: this is what I want, stated directly, with none of the near-misses laid out beside it.

This is also why a model recording its own mistakes back into a prompt, or into a note it keeps, can quietly make things worse. The record lands in the model's context as one more example, and the copying mechanism does not distinguish a cautionary example from a target one; both are just patterns available to be continued. A well-meant note that spells out the behavior you objected to is, to the machinery, a fresh demonstration of it, reinforcing the behavior rather than suppressing it.

The rule follows straight from the mechanism. Repair by replacement, not by annotation: cut the wording you have rejected instead of demoting it to a bad example, because to a pattern-continuing model there is no such thing as an example that is merely shown and not, at least a little, learned from.

---

## Glossary

I was a bit lazy about this section to be honest. I left most of it up to AI and probably should have given it a more critical eye. Try not to get bored if you are forced to dive into these and I apologise in advance for any mistakes contained herein and the dreary greyness of most of it.

**Attention (self-attention)**

For every token, attention works out how much each other token in the window should influence it. Each token is turned into three vectors: a query, a key, and a value. The pull of token B on token A is the query of A compared against the key of B, run through a softmax so the weights over all tokens add to one, and A's updated representation is the weighted blend of every token's value. This is how the model links a pronoun to its noun, or an instruction to the text it governs. Attention has no built-in sense of order, which is why positional encoding is needed on top of it.

**Attention head (multi-head attention)**

Attention, the mechanism a model uses to weigh how much each earlier token should influence the next, does not run just once; it runs many times in parallel, and each parallel copy is called a head. Different heads learn to track different relationships, one might follow which noun a pronoun refers to, another which word tends to follow which. Running them together is multi-head attention. The idea matters because specific, nameable behaviors often live in specific heads, which is how researchers can point to, say, the heads responsible for copying patterns out of the prompt.

**Autoregressive generation**

The strictly left-to-right way a model produces text: one token at a time, each new token conditioned on every token before it, including the ones it has just written. The model does not go back and revise, and it keeps no separate scratchpad, so the only working memory it has for a task is the visible text so far. This is why writing reasoning out helps, and why very long outputs can drift off course.

**Byte-pair encoding (BPE)**

The most common recipe for building a tokenizer's vocabulary. Beginning from single characters, it repeatedly merges the most frequent adjacent pair into a new symbol until it reaches a set vocabulary size. Frequent words survive as single tokens, while rare words, names, code, and other languages are broken into several pieces. This is why token counts never line up neatly with word counts.

**Chain-of-thought**

Reasoning written out as tokens instead of performed silently. Because a model's only working memory is the text in front of it, having it set down intermediate steps gives it somewhere to hold partial results and to notice its own mistakes, which measurably improves multi-step work. Reasoning models are trained to generate a long chain-of-thought on their own before answering.

**Conditioning**

The way a model's output is shaped by the text around it. At each step the model predicts what comes next given everything currently in its context, so any instruction, example, or reason you include bends those predictions while it is present and stops mattering the moment it leaves the context. Conditioning is why a prompt works at all, and why its effect is temporary rather than a lasting change to the model.

**Constrained decoding**

Restricting which tokens the model is allowed to emit at each step so the output must fit a grammar, a schema, or a fixed set of choices. Unlike a prompt, which only makes an outcome more likely, this is enforced outside the model's preferences, which makes it one of the few ways to guarantee a format rather than merely encourage one.

**Context window (context length)**

The fixed maximum number of tokens a model can take in for a single pass. It is shared, all at once, by the system prompt, the whole conversation so far, the current input, and the reply being generated. Anything past it is invisible to the model, which is the real reason it appears to forget the earlier parts of a long session.

**Defense in depth**

The security practice of protecting something with several independent layers, so that no single failure is enough to cause harm. In an AI system it means not relying on a prompt instruction alone for anything important, but stacking real safeguards behind it: a permission check, a human approval step, a limited set of allowed actions. The prompt may still fail; the layers behind it are what keep that failure from doing damage.

**Distribution shift**

What happens when the inputs a model meets in the wild drift away from the examples it was tuned on. Behavior that was dependable on those examples turns patchy as real cases diverge from them. It is the technical reason a rule pinned to one specific anecdote can misfire once a new situation only half matches the story.

**Embedding**

A stretch of text converted into a list of numbers, a vector, placed so that passages with similar meaning sit near one another in that numeric space. Because closeness stands in for similarity of meaning, embeddings let software compare texts by distance, which is the foundation of vector search and retrieval-augmented generation.

**Emergence**

The observation that certain abilities show up in a model only once it passes some size or amount of training, rather than improving smoothly all the way up. A small model may fail a kind of task completely while a larger one suddenly handles it. How sharp and how real these jumps are is debated, and some look like artifacts of how the ability is measured, but the practical point holds: a task a given model simply cannot do is not something more prompting will fix.

**Few-shot prompting (in-context learning)**

Placing a few worked examples of the behavior you want directly in the prompt so the model infers the pattern and carries it on, with no change to its weights. The catch is that any example in the prompt is a pattern the model may copy, including a bad one you only meant as a warning.

**Fine-tuning**

A round of extra training applied to a model after its initial learning from a large body of text, using curated examples so it behaves the way you want. The most common kind is instruction tuning: teaching the model to follow instructions from examples of requests paired with good responses. Fine-tuning adjusts the model's weights, unlike prompting, which changes nothing permanent and lasts only as long as the text stays in the context window.

**Forward pass**

One complete sweep of the model's computation over its current input, running from the tokens in the context window through to a score for every possible next token. Producing a reply is a sequence of forward passes, one for each token generated. It is the unit of work behind every answer, and everything in the prompt takes part in each pass.

**Generalization**

A model's ability to take a pattern it has learned or been shown and apply it to new cases that resemble it, rather than only to the exact examples it has seen. Models generalize by holding meaning as patterns of numbers and treating nearby patterns alike, which is what lets a broadly stated rule reach situations you never named. It is also why an overly specific instruction travels poorly: there is little for the model to extend.

**Hallucination**

Fluent, confident output that is not grounded in anything real. Since a model always produces the most plausible continuation and cannot tell absent context from nonexistent context, it fills the gap with invented specifics that read as authoritative. It is a failure of grounding rather than of fluency. It is never uncertain about any of this; being uncertain would mean noticing that something was missing, which is the one thing it cannot do.

**Hard vs. soft constraint**

A soft constraint is anything expressed in the prompt: it tilts the model's probabilities and holds most of the time, but it can fail. A hard constraint is enforced by the surrounding software, such as a permission check, a validator, or constrained decoding, and does not rely on the model's cooperation at all. Anything you cannot afford to have fail belongs in the hard category.

**In-context memory**

What the model knows purely because it is in the context window at this moment, as opposed to the parametric memory held in its weights. It disappears the instant those tokens leave the window, and it is the only memory you can add to on the fly.

**Induction heads**

A particular kind of attention head, found across many models, that carries out copying from context. It spots a place earlier in the text where the current token already appeared, looks at what followed it there, and raises the odds of producing that same continuation now. Induction heads are a large part of why a model can pick up a pattern shown in the prompt and continue it, and also why any text left in a prompt, a bad example included, is liable to be reproduced.

**Inductive bias**

The set of built-in leanings, inherited from a model's architecture and its training, that decide which patterns it reaches for first when it generalizes. Two systems shown the same examples can reach different conclusions because their inductive biases differ. In prompting, it is why a broad, plainly stated principle tends to land as you intend, while an unusual or narrowly framed one is easier for the model to misread.

**Instruction hierarchy**

The order of trust a model is trained to apply to the different instructions in its context, typically ranking a developer's system prompt above the user's messages, and both above text that arrives from tools or documents. It usually holds, but it is a learned tendency rather than a hard rule, so a confusing or hostile input can still override it. It is why the same words carry more force in a system prompt than when buried in a web page the model happened to read.

**KV cache**

A speed optimization used while a model generates text. As it processes each token, the model computes and stores a small pair of summary vectors for that token, the key and value vectors that attention reuses, so every later token can look back at it without redoing the work. The detail worth knowing is that once a token is in the cache it is fixed: the model builds on what it has already produced, including any early mistake, rather than reconsidering it.

**Logits and softmax**

At each step the model emits a raw score, called a logit, for every token in its vocabulary. Softmax turns those scores into a probability distribution that sums to one, and the next token is then drawn from that distribution. Prompts, temperature, and similar controls all act by reshaping these numbers.

**Lost in the middle**

A well-documented tendency of language models to use information at the very start and the very end of a long context more reliably than information buried in the middle. Because attention is spread across everything in the window, a crucial instruction sitting halfway down a long prompt can carry less weight than the same instruction near the top or bottom. It is a practical reason to keep important instructions short, prominent, and clear of filler. It reads a long prompt rather the way a tired human reads a long contract: the opening, the closing, and a hopeful glance at the middle.

**Parameters (weights)**

The billions of numbers learned during training and then held fixed while the model runs. They hold the model's general skills and world knowledge, its parametric memory. Talking to the model does not alter them; only further training or fine-tuning does.

**Positional encoding**

Any scheme that tells the model where each token sits, since attention on its own is blind to order. Earlier models added fixed position signals into the token representations; most current models use Rotary Position Embedding instead.

**Priming**

The tendency of any concept you mention to become more active, and so more likely to surface, whether or not you framed it as something to avoid. It is the mechanism behind being unable to not think of a pink elephant, and the reason negations and leftover bad examples can quietly backfire.

**Prompt injection**

An attack in which text from an untrusted source, such as a web page, a file, or a message, carries its own instructions that the model may follow in place of yours, because the model treats every piece of text in its context as input. It is a central reason not to trust prompt wording to guard critical actions.

**Reasoning model**

A model trained, usually with reinforcement learning, to produce an extended internal chain-of-thought before its final answer. That reasoning pass lets it break problems down, check itself, and follow instructions, including negations, more reliably than a model that replies in a single shot. The underlying machinery is still next-token prediction; the reasoning is generated the same way as any other text. The thinking is not a grander act than the talking; it is the same next-token guesswork, in a lab coat.

**Reinforcement learning**

A training stage that shapes behavior by rewarding good outputs rather than copying fixed examples. It is how models are taught to follow instructions and stay helpful, often called RLHF when the reward comes from human preferences, and how reasoning models are trained to reason toward correct answers. Reward the paths that lead to good outcomes and the model produces more of what earned the reward.

**Representation**

The model's internal stand-in for a piece of meaning, held as a pattern of numbers (a vector) rather than as words. Reading a phrase activates the representations of the concepts involved, and a concept whose representation is active is more likely to surface in the output, which is the mechanism behind priming. Similar meanings have representations that sit close together, which is also what makes embeddings and semantic search possible.

**Retrieval-augmented generation (RAG)**

A pattern for giving a model knowledge it was never trained on by searching an external store for relevant passages and pasting them into the context window before the model runs. The model still knows only what is in the window; RAG is a disciplined way of putting the right material there. It usually leans on embeddings and a vector database.

**Rotary Position Embedding (RoPE)**

The positional-encoding scheme used by most current models. It rotates each token's query and key vectors by an angle proportional to the token's position, so the comparison between two tokens depends on the difference between their angles, giving the model a feel for relative distance. It also makes attention taper off gently with distance, and it is why a model cannot be run far past its trained length without adjustment.

**Sampling (temperature, top-p, top-k)**

How the next token is chosen from the probability distribution. Greedy decoding always takes the single most likely token; temperature flattens or sharpens the distribution, with higher values allowing more randomness; top-k confines the choice to the k most likely tokens, and top-p to the smallest group whose probabilities add up to p. These settings, not the prompt alone, decide how varied or repeatable the output is.

**Scaling**

The broad finding that models tend to get more capable in fairly predictable ways as you increase three things together: the number of parameters, the amount of training data, and the computation spent on training. It is why capability is not uniform across models, and why some jobs call for a larger model rather than a cleverer prompt. Scaling lifts general competence; it does not remove the need to match a task to a model that can handle it.

**Stateless**

The property that every request is handled on its own, with nothing retained from the ones before. The continuity of a chat is an illusion the surrounding application creates by resending the entire transcript each turn. Nothing you said earlier reaches the model unless it is sitting in the current context window.

**System prompt**

The standing, behind-the-scenes instructions set by whoever built an application and sent to the model ahead of the user's messages to fix its role, rules, and tone. The user usually never sees it, and models are trained to weight it above ordinary user input. It is where developers put durable guidance, but because it is still only text in the context, it biases behavior rather than enforcing it.

**Token**

The atomic unit a model reads and writes: a chunk of text usually smaller than a word, produced by the tokenizer. Almost every context limit and most pricing is counted in tokens, and one English token averages about four characters, or roughly three-quarters of a word. It is also why a model can write a fluent essay about a word yet miscount the letters in it: it never quite saw the letters, only the chunk.

**Tokenizer**

The component that turns text into tokens before the model sees it, and back into text on the way out. Its fixed vocabulary is usually built with byte-pair encoding, and because it works in subword pieces, exactly how a string splits can affect both cost and, now and then, behavior.

**Tool calling (function calling)**

The mechanism that lets a model do more than produce text. Instead of an answer, it emits a structured request to run a named operation, and separate software around it, an orchestrator, carries that request out and feeds the result back. This is how models search the web, query a database, or send a message. It is also where prompt rules become risky to rely on, since a tool that can take a real action is only as safe as the checks built around it, not the wording asking the model to be careful.

**Vector**

A list of numbers, treated as a single point in a space with many dimensions. Models represent almost everything this way: each token, each concept, and each stored summary is a vector, and the closeness of two vectors stands in for how related the two things are. Nearly every other term here, from embeddings to attention's query, key, and value vectors, is built on this one idea.

**Vector database**

A store designed to hold embeddings and answer, quickly, which stored items are nearest to a given one, using distance in the numeric space as a stand-in for similarity of meaning. It is the retrieval half of a RAG system.

**Vocabulary**

The fixed set of tokens a model can read and write, decided when its tokenizer is built. Every step of generation produces a score for each token in this vocabulary and then picks one, so the vocabulary is the full menu of pieces the model has to work with. Sizes commonly run into the tens or hundreds of thousands of tokens.

**YaRN**

A method for extending a model's usable context beyond the length it was trained on. Short for yet another RoPE extensioN, it rescales RoPE's rotation frequencies unevenly, leaving the fine-grained high-frequency components largely intact while stretching the coarse ones, and adjusts attention to match, so quality holds up at several times the original length. It is what people mean when they say a model was extended to a longer context. The name is not me being flippant; the researchers really did call it Yet Another RoPE extensioN, and the weariness in that is its own small documentation. 

```
# Prompt Authoring Instructions for AI

## Purpose

This document instructs an AI on how to write prompts. Apply it whenever you draft, revise, or review a prompt. It is complete on its own: every term, principle, and step you need is defined below.

## Definitions

These four terms carry precise meanings that the principles and verification checklist depend on.

- **Positive specification**: an instruction stated as the behavior to perform.
- **Standing reason**: a justification that holds true in every situation where its rule applies, stated as a general principle.
- **Technical safeguard**: an enforcement mechanism built into the software around the model — a permission check, a human approval step, or a restricted set of available actions — that operates independently of how the model reads or weighs any wording.
- **Self-contained**: fully actionable by a stranger whose only context is this one document.

## The Eight Principles

Apply all eight to every prompt you write.

**1. State each instruction as the behavior to perform.**
Write every rule as a direct description of the desired action, output, or scope. Small negating function words receive little attention weight and can drop out of effect, while naming a behavior activates the model's internal representation of it and raises the odds of producing it. Positive specification keeps the idea you activate and the outcome you want identical.

**2. Make the prompt self-contained.**
Write every term, format, example, and decision the model needs into the prompt itself. A model begins each request holding only the text in front of it, and it fills any gap with a plausible invention presented as fact. Test each prompt against the definition of self-contained above.

**3. Give standing reasons.**
When a rule benefits from a why, phrase the reason as a general principle stating the underlying cost or benefit. A standing reason travels with the rule into situations the author left unnamed, and it survives future edits because the rule and its justification live in a single sentence.

**4. Number ordered procedures.**
When a task must happen in a specific order: number every step, state the total, make each step one complete action that finishes before the next begins, and end with a verification step confirming every earlier step was completed. Written numbering turns the plan into visible tokens the model can count itself against, and the closing verification forces a reread in which a skipped step becomes visible text.

**5. Word rules with calibrated force.**
Write each rule as a clear, strong instruction in plain sentence case. A prompt shifts the probability of behavior; calibrated wording keeps everyone's expectations aligned with that reality and keeps attention on principle 6 for the cases where certainty is required.

**6. Put critical protections in technical safeguards.**
For any action that is irreversible, or where a single failure causes serious harm, require a technical safeguard and record which one applies. Reserve the prompt for judgment guidance. A safeguard holds in exactly the situations where wording gives way: long contexts, competing instructions, and hostile input arriving through the text the model reads.

**7. Include only what is true every time.**
Every sentence in a prompt must hold in all its uses. Leave situational decisions to the model at run time. Each token competes with every other for attention, so a lean prompt gives full weight to the rules that always matter, and rules that always hold stay consistent with each other. Match the task to the model's capability; when a task exceeds it, shrink the task or use a stronger model.

**8. Repair by replacement.**
When revising a prompt, delete superseded wording entirely and write the current target in its place. Every line present in a prompt is a pattern the model can imitate, whatever label sits beside it, so the finished prompt contains only wording you want reproduced.

## Authoring Procedure

Follow these seven steps in order. Each step is a single, complete action.

1. **Step 1 of 7 — Collect requirements.** Write down the task, the target model, the output format, and every decision the prompt must fix in advance.
2. **Step 2 of 7 — Sort the constraints.** Assign each requirement to one of two lists: judgment guidance, which goes in the prompt, or critical protection, which is flagged for a technical safeguard with the specific mechanism named (principle 6).
3. **Step 3 of 7 — Draft with positive specification.** Write every instruction as the behavior to perform (principle 1), admitting only requirements that are true in every use (principle 7).
4. **Step 4 of 7 — Attach standing reasons.** Add a general-principle reason to each rule whose purpose is unclear from its wording alone (principle 3).
5. **Step 5 of 7 — Structure ordered work.** Convert each order-dependent task inside the prompt into numbered steps with a stated total and a closing verification step (principle 4).
6. **Step 6 of 7 — Make it stand alone.** Define every term, embed every format and example, and confirm the prompt passes the self-contained test (principle 2).
7. **Step 7 of 7 — Verify.** Reread the finished prompt and confirm each item below is true:
   - Every instruction states a behavior to perform.
   - Every term, format, and example the model needs appears in the prompt.
   - Every stated reason is a general principle.
   - Every ordered task is numbered, states its total, and ends with verification.
   - Every rule is worded with calibrated force in plain sentence case.
   - Every irreversible or high-harm action has a named technical safeguard.
   - Every sentence is true in all uses of the prompt.
   - The prompt contains only current, intended wording.
   - Steps 1 through 6 of this procedure were each completed.

## Scope of This Document

Following this document makes well-formed prompts the reliable outcome. For any outcome that must hold every single time, a technical safeguard is the mechanism that provides it.
```
