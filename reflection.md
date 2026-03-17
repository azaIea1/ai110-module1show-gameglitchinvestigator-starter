# 💭 Reflection: Game Glitch Investigator

Answer each question in 3 to 5 sentences. Be specific and honest about what actually happened while you worked. This is about your process, not trying to sound perfect.

## 1. What was broken when you started?

- What did the game look like the first time you ran it?
- List at least two concrete bugs you noticed at the start  
  (for example: "the secret number kept changing" or "the hints were backwards").

---

Q1 Answer: I notcied a few bugs were hints working backwards, I was inputing negative numbers and it was telling me to go lower when the target was higher, another thing was
when you won starting a new game a new game started but I wasnt allowed to submit my guesses. Lastly I wasnt too sure if this was a bug but it makes no sense that your able to guess numbers
that are less than one and greater than 100. 

## 2. How did you use AI as a teammate?

- Which AI tools did you use on this project (for example: ChatGPT, Gemini, Copilot)?
- Give one example of an AI suggestion that was correct (including what the AI suggested and how you verified the result).
- Give one example of an AI suggestion that was incorrect or misleading (including what the AI suggested and how you verified the result).

---

I used Claude AI throughout the project. One example of a correct AI suggestion was when it identified that the hint messages were swapped in `check_guess` — it pointed out that when `guess > secret` the code was saying "Go HIGHER" instead of "Go LOWER". I verified this by manually playing the game with the debug panel open, guessing a number I knew was higher than the secret, and confirming the wrong hint appeared.

One example of an AI suggestion that was misleading was when Claude initially focused only on the message strings being swapped inside `check_guess` as the root cause of wrong hints. In reality the deeper bug was in `app.py`, where the secret was being converted to a string on every even attempt using `str(st.session_state.secret)`. This caused string comparison instead of numeric comparison, so hints were wrong every other guess regardless of the message fix. I caught this by reading through `app.py` line by line after the first fix didn't fully solve the problem.

## 3. Debugging and testing your fixes

- How did you decide whether a bug was really fixed?
- Describe at least one test you ran (manual or using pytest)
  and what it showed you about your code.
- Did AI help you design or understand any tests? How?

---

I decided a bug was really fixed by testing it both in `pytest` and by manually playing the game. For pytest I ran the three tests in `tests/test_game_logic.py` which checked that `check_guess(50, 50)` returns `"Win"`, `check_guess(60, 50)` returns `"Too High"`, and `check_guess(40, 50)` returns `"Too Low"`. All three passed after the refactor, which confirmed the core logic was correct. I also manually tested the live game by opening the debug panel, noting the secret number, and submitting guesses above and below it to verify the hints were accurate every single round, not just on odd attempts. Claude helped me understand what the tests were checking and pointed out that the return type of `check_guess` needed to be just a string rather than a tuple to match what the tests expected.

## 4. What did you learn about Streamlit and state?

- In your own words, explain why the secret number kept changing in the original app.
- How would you explain Streamlit "reruns" and session state to a friend who has never used Streamlit?
- What change did you make that finally gave the game a stable secret number?

---

The secret number kept changing because every time you clicked a button, Streamlit re-ran the entire `app.py` script from top to bottom, which meant `random.randint()` was called again and generated a new number every single click. It's basically like the script never remembers anything between interactions. I would explain it to a friend like this: imagine every time you press a button on a website, the whole page reloads from scratch and forgets everything — that's what Streamlit does by default. `session_state` is like a sticky note that survives those reloads, so you can store things like the secret number and it won't reset. The fix was wrapping the `random.randint()` call in a check — `if "secret" not in st.session_state` — so a new secret only gets generated once at the start of a new game, not on every rerun.

## 5. Looking ahead: your developer habits

- What is one habit or strategy from this project that you want to reuse in future labs or projects?
  - This could be a testing habit, a prompting strategy, or a way you used Git.
- What is one thing you would do differently next time you work with AI on a coding task?
- In one or two sentences, describe how this project changed the way you think about AI generated code.

---

One habit I want to carry forward is reading through the actual code line by line even after the AI gives me a fix, because the string conversion bug in `app.py` was something I only caught by doing that — the AI's first explanation didn't surface it. Next time I work with AI on a coding task I would give it more context upfront, like sharing both files at once instead of one at a time, so it can see how the pieces connect before suggesting a fix. This project changed how I think about AI-generated code because I used to assume if it ran without errors it was probably correct, but this showed me that code can run fine and still have logic bugs that only show up in specific situations.

