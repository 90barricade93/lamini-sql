# Lamini: Create Your Own ChatGPT For SQL
[![License](https://img.shields.io/badge/License-CC%20By%204.0-green.svg)](/LICENSE.txt)
[![Python 3.7+](https://img.shields.io/badge/python-3.7+-blue.svg)](https://www.python.org/downloads/release/python-370/)
[![Code style: black](https://img.shields.io/badge/code%20style-black-000000.svg)](https://github.com/psf/black)

This is the repo for the Lamini project, which aims to build and share an instruction-following model with CC-BY license that allows commercial use. The repo contains:

- The [52K data](#data-release) used for finetuning your own instruction-following SQL tuned LLM, a la ChatGPT.
- The code for [generating the data](#run).

See our [blogpost](https://lamini.ai/blog) for layperson's terms on what's going on.

![Lamini Process Step by Step](assets/process.png "Lamini Process Step by Step")

🦙🐪🦙🐫🦙🐪🦙🐫🦙🐪🦙🐫🦙🐪🦙🐫🦙🐪🦙🐫🦙🐪🦙🐫🦙🐪🦙🐫


## Authentication to Lamini

Ready to configure your API key? It's easy-peasy! 🔑

First, navigate to your [Lamini account page](https://app.lamini.ai) to retrieve your unique API key. Remember to keep this key a secret, and don't expose it in any client-side code or share it with others.

Next, create a config file, like so:

```
mkdir ~/.powerml
touch ~/.powerml/configure_llama.yaml # backend system names
```

Finally, open the file with a text editor and place your key in it:

```
production:
    key: "<YOUR-KEY-HERE>"
```

The best part? The [Lamini python package](https://pypi.org/project/llama-llm) will automatically load your key from this config file for you, so you don't have to worry about it 🙌

If you're running Lamini in a docker container, make sure to copy/mount this file inside the container 🐳

See our [API docs](https://lamini-ai.github.io/auth/) for more details.

## Run
Clone the repository:
```
git clone git@github.com:lamini-ai/lamini-sql.git
```

### Using Python 🐍

In the repository, install python dependencies:

```
pip install -r requirements.txt
```

Run the program, to start generating data 📊📊📊

```
python3 generate_data.py
```

### Using Docker 🐳

Make sure you have [docker](https://docs.docker.com/get-docker/) installed.

Then, run this command:

```bash
./run_generate_data_docker.sh
```

## Expected Outputs & Autosaved Data 🦙
When you run the program, you should start seeing output of a `Seed Question`, from the original small dataset in [`train_spider.json`](./data/spider/train_spider.json), and a `Novel Question`, which is a generated question based on that `Seed Question`.[^1]
[^1]: The `Seed Questions` in the Lamini seed dataset are instructions (combination of questions and commands), based on the [spider](https://yale-lily.github.io/spider). The generated questions are similar in nature to those and therefore don't *have to* be questions. You can find the seed dataset at [`data/spider/train_spider.json`](./data/spider/train_spider.json).
```
====== Seed Question =====
 question='Show all movie titles, years, and directors, ordered by budget.'
===== Novel Question =====
 question='What are the names, birthdays and addresses of the 10 customers with the most orders?'
```
These generated questions are saved to `data/questions.jsonl`. This JSON file is a list of dictionaries with a `question` field.

Next, you'll see a `Response` generated for each `Novel Question`.
```
====== Question =====
 question='How many heads of the departments are older than 56 ?'
===== Query =====
 response='SELECT count(*) FROM head WHERE age  >  56'
```

These pairs are saved to `data/dataset.jsonl`. This JSON file is a list of dictionaries with `question` and `query` fields.

It's poggers 💥

## Modify

### I want to use my own seed data
We suggest creating your own dataset and changing the path to the [`train_spider.json`](./data/spider/train_spider.json) in `generate_data.py`(./generate_data.py) --- or you can replace  [`train_spider.json`](./data/spider/train_spider.json) with your own data in the same format. You can of course also modify how the data is loaded or write your own script with the `llama-llm` library (pssst, [API docs](https://lamini-ai.github.io/auth/)).

### I only want to generate questions (to start)
In `generate_data.py`(./generate_data.py), you can just run `generate_questions`. This is a common use case for using human review after the question generation step to filter only the good ones for the next step of generating a response for each question.

### I have my own instructions, and just want to generate responses
In `generate_data.py`(./generate_data.py), you can just use the function `make_pairs` to create the question-response pairs. This is a common use case step to run this stage separately, e.g. after human review of the generated questions, or if there was an error at this step last time.

### I want to generate more than 100 instructions
Change the count flag `-c` for the number question-repsonse pairs to generate in total. The default is set to 100.

## Data Release
We've run this script a few times and saved the results for you to freely use, at [`data/lamini_dataset.jsonl`](./data/lamini_dataset.jsonl) 💸

This file contains 52K instruction-following data for commercial use (ie. feel free to use it for your business! 💰📈). It's the same as the output, a list of dictionaries, each of which contains the following fields:
- `question`: `str`, describes the task the model should perform. Each of the 52K instructions is unique, as generated by `lamini/open`.
- `query`: `str`, the answer to the instruction as generated by `lamini/open`.

## About Lamini
[Lamini](https://lamini.ai/) is the world's most powerful LLM engine, unlocking the power of generative AI for every company by putting their data to work. It is based on the [lamini tribe](https://en.wikipedia.org/wiki/Lamini), which includes llamas (LLMs!), alpacas, etc.
