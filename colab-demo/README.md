# Creative ideas in Google Colab

`creative_ideas.ipynb` asks for an everyday object and uses the OpenAI Responses API with `gpt-6-luna` to suggest **three numbered unusual, practical uses**, each with a short explanation.

## Run the notebook

1. Open [Google Colab](https://colab.research.google.com/), choose **File > Upload notebook**, and upload `creative_ideas.ipynb`.
2. Run the cells from top to bottom using each cell's play button. The first code cell installs or updates the OpenAI Python SDK in the Colab runtime.
3. When prompted, enter your OpenAI API key in the hidden `getpass` input. Get a key from the [OpenAI API dashboard](https://platform.openai.com/api-keys). Your account needs API billing/credits and access to `gpt-6-luna`; requests may cost money.
4. Enter an object, such as `paper clip` or `empty jar`, and read the three numbered ideas. Keep entering new objects without re-entering the key. Type `quit` or `exit` at the object prompt to stop. Blank input prompts you to try again without making an API request.

## Keep your key private

Enter the key only in the runtime prompt. Never paste it into a code or Markdown cell, print it, save it to a file, or commit it to Git. The notebook passes it directly to the SDK in memory and does not display it or write it to disk. Error messages deliberately omit exception details. Disconnect and delete the Colab runtime when finished. Clear notebook outputs before saving or sharing.

## How it works

The notebook sends the object as input to `client.responses.create(...)`. Instructions ask for exactly three distinct unusual, practical uses in a numbered list (1, 2, 3), with one or two sentences per idea, and `response.output_text` contains the answer. The notebook is saved with no executed cells or outputs.

See the [official Responses API reference](https://developers.openai.com/api/reference/python/resources/responses/methods/create).

`hello_colab.ipynb` is the existing introductory notebook.
