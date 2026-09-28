# Voice guide: how I talk upstream

DRAFT for Branden to edit. These are rules about how I write, so the wording should
end up being mine.

## Who I am in threads

I am new to open source and this is among my first contributions. I come from data
analytics and I am moving into AI engineering, so I read code carefully and I am
comfortable with data shapes, pipelines, and edge cases, but I do not know this
codebase's history and I will not pretend otherwise. My workflow is AI-assisted, and
I run and verify everything I post. What a maintainer can expect from me is that
anything I state as fact, I actually ran, and anything I am guessing, I label as a
guess.

## Rules I write by

### Rule: Promise the investigation, never the fix

When I claim an issue I say what I am going to look into and that I will report back.
I do not promise a pull request, a solution, or a date, because I do not yet know how
deep the problem goes and a missed promise costs a maintainer more than no promise.

- Wrong: "Claiming this one. I'll have a PR up with the fix by Tuesday."
- Right: "I'd like to take a look at this. My plan is to trace where `file_structure`
  is supposed to be set and report what I find before proposing anything."

### Rule: Show the output, do not describe it

If I say something happened, the thing I ran and what it printed goes in the comment.
A pasted artifact is worth more than any sentence I could write about it, and a
sentence without one is not evidence.

- Wrong: "I can confirm this is reproducible, both flags come back False every time."
- Right: "Running the analyzer on the dict `GitHubTool` builds: `has_tests = False`,
  `has_ci = False`, output pasted below."

### Rule: Record where I ran it, every time

Every report starts with the OS, the Python version, and the commit I was on. I have
been on the receiving end of numbers with no context in analytics work, and a result
with no environment is a number with no context.

- Wrong: "Reproduced on my machine."
- Right: "macOS 15.x (arm64), Python 3.11.3, on commit `<sha>` of `main`."

### Rule: Say plainly what I do not know

If I have a hypothesis about the cause, I call it a hypothesis. I would rather be
visibly uncertain than be confidently wrong in a thread a maintainer has to read.

- Wrong: "The root cause is that `GitHubTool` was refactored and the key got dropped."
- Right: "I haven't traced why the key is missing rather than stale. What I can show
  is that nothing in the codebase writes it."

### Rule: Write like a person, not like a generated comment

Plain sentences, ordinary words, commas instead of em-dashes. No throat-clearing
openers, no "I hope this helps", no bulleted summary of a three-line comment. If a
line reads like it came out of a template, it goes.

- Wrong: "Great catch on this issue! I'd be happy to dive deep into this and help
  drive it to resolution."
- Right: "Looks like the analyzer reads a key nothing sets. I'll dig into it."

## Things I never post

- A date, or a promise of a fix, or "should be easy".
- "Same as above, can confirm." My proof goes up in my own words or not at all.
- A root cause I have not shown evidence for.
- "Please assign me" with nothing else in the comment.
- Anything I have not actually run on my own machine.
- Emoji, exclamation marks, or enthusiasm I do not feel.
