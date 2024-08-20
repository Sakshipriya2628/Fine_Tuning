# Fine_Tuning
Here's a README document for the fine-tuning code:

---

# Fine-Tuning GPT-3.5 Turbo with OpenAI API

This document provides instructions and an overview of the process for fine-tuning the GPT-3.5 Turbo model using the OpenAI API.

## Prerequisites

1. **Python**: Ensure you have Python installed (version 3.7 or later).
2. **OpenAI Python Client**: Install the OpenAI Python client by running the following command:
   ```bash
   pip install openai
   ```
3. **OpenAI API Key**: Obtain your OpenAI API key from your OpenAI account and set it as an environment variable:
   ```bash
   export OPENAI_API_KEY="your-api-key"
   ```

## Fine-Tuning Process

### 1. Prepare Your Data

The data used for fine-tuning must be in the `.jsonl` format. Each line in the file should be a valid JSON object containing the prompt and the expected completion.

Example of a single line in `merged_output.jsonl`:
```json
{"prompt": "<Your prompt here>", "completion": "<Your desired output here>"}
```

### 2. Upload the Data File

Upload your prepared `.jsonl` file to OpenAI using the following code:

```python
import os
import openai

# Set your OpenAI API key
openai.api_key = os.getenv("OPENAI_API_KEY")

# Upload the file to OpenAI
response = openai.File.create(
  file=open("merged_output.jsonl", "rb"),
  purpose='fine-tune'
)

# Store the file ID for future use
file_id = response['id']
print(f"File ID: {file_id}")
```

### 3. Create the Fine-Tuning Job

With the file uploaded, initiate the fine-tuning job:

```python
# Replace "file-xxxxxxxxx" with your actual file ID
fine_tuning_job = openai.FineTuningJob.create(
  training_file="file-BlcgTaVU6rX2ujCoCSIC0DxU",
  model="gpt-3.5-turbo"
)

# Retrieve the fine-tuning job ID
fine_tuning_job_id = fine_tuning_job['id']
print(f"Fine-Tuning Job ID: {fine_tuning_job_id}")
```

### 4. Monitor the Fine-Tuning Job

To check the status of your fine-tuning job, you can retrieve its details:

```python
# Replace "ftjob-xxxxxxxxx" with your actual fine-tuning job ID
job_status = openai.FineTuningJob.retrieve("ftjob-D3ObjDnScCKTXWlQDn7m9Hv4")

print(job_status)
```

### 5. Use the Fine-Tuned Model

Once the fine-tuning process is complete, you can use your fine-tuned model for inference:

```python
completion = openai.ChatCompletion.create(
  model="ft:gpt-3.5-turbo-0613:blenheim-chalcot::7rQyrPOz",
  messages=[
    {"role": "system", "content": "Give me the sentence analysis of which category is it."},
    {"role": "user", "content": "The weather is hot"}
  ]
)

# Print the response from the fine-tuned model
print(completion.choices[0].message["content"])
```

### 6. Additional Notes

- **Model Name**: The model name in the `ChatCompletion.create` call should be replaced with your fine-tuned model's name, which you will receive upon job completion.
- **Error Handling**: Ensure you include proper error handling in a production environment, especially for API rate limits and timeouts.
