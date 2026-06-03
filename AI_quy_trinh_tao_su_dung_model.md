### Yêu cầu bài toán: Bạn vẽ sơ đồ từ FastAPI toi Hugging Face Transformers -> toi Pytorch traning -> rồi làm sao dùng model này để tạo 1 con AI agent

<img width="932" height="437" alt="image" src="https://github.com/user-attachments/assets/d1dd210e-8054-4d4d-b4dd-d75c2c6dc81e" />
<img width="928" height="632" alt="image" src="https://github.com/user-attachments/assets/78ab578b-4c6c-4991-9b59-aa6a368800db" />
<img width="986" height="683" alt="image" src="https://github.com/user-attachments/assets/18bcdd70-1a04-4faa-b7a1-5def1f786496" />
<img width="930" height="660" alt="image" src="https://github.com/user-attachments/assets/56856f2c-7f45-4a58-a2c1-5966e6b72da7" />

## Ví dụ code Python:

```
from transformers import AutoModelForCausalLM, AutoTokenizer
from peft import LoraConfig, get_peft_model
import torch

# 1. Load mô hình nền (ví dụ LLaMA)
model_name = "meta-llama/Llama-2-7b-hf"
tokenizer = AutoTokenizer.from_pretrained(model_name)
model = AutoModelForCausalLM.from_pretrained(
    model_name,
    device_map="auto",
    torch_dtype=torch.float16
)

# 2. Cấu hình LoRA
lora_config = LoraConfig(
    r=8,              # rank
    lora_alpha=32,    # scaling
    target_modules=["q_proj","v_proj"],  # các module cần chèn LoRA
    lora_dropout=0.05,
    bias="none",
    task_type="CAUSAL_LM"
)

# 3. Gắn LoRA adapter vào mô hình
model = get_peft_model(model, lora_config)

# 4. Chuẩn bị dữ liệu (ví dụ dataset text)
from datasets import load_dataset
dataset = load_dataset("yelp_review_full")

# Tokenize
def tokenize_function(examples):
    return tokenizer(examples["text"], truncation=True, padding="max_length", max_length=512)

tokenized_dataset = dataset.map(tokenize_function, batched=True)

# 5. Training với Hugging Face Trainer (PyTorch backend)
from transformers import TrainingArguments, Trainer

training_args = TrainingArguments(
    output_dir="./results",
    per_device_train_batch_size=2,
    num_train_epochs=1,
    fp16=True,
    logging_steps=10,
    save_steps=500,
    save_total_limit=2
)

trainer = Trainer(
    model=model,
    args=training_args,
    train_dataset=tokenized_dataset["train"],
    eval_dataset=tokenized_dataset["test"]
)

trainer.train()
```

<img width="936" height="500" alt="image" src="https://github.com/user-attachments/assets/947657d4-d960-4589-8f69-5341d0703ac6" />
<img width="986" height="348" alt="image" src="https://github.com/user-attachments/assets/c492adff-c6be-49a4-9f14-0fea86931264" />

```
from transformers import AutoModelForCausalLM, AutoTokenizer
from peft import PeftModel

class MyAgent:
    def __init__(self, base_model_name, lora_checkpoint):
        self.tokenizer = AutoTokenizer.from_pretrained(base_model_name)
        base_model = AutoModelForCausalLM.from_pretrained(base_model_name)
        self.model = PeftModel.from_pretrained(base_model, lora_checkpoint)

    def chat(self, prompt):
        inputs = self.tokenizer(prompt, return_tensors="pt")
        outputs = self.model.generate(**inputs, max_length=200)
        return self.tokenizer.decode(outputs[0], skip_special_tokens=True)
```

<img width="948" height="123" alt="image" src="https://github.com/user-attachments/assets/226a75c6-fd28-4048-b8a9-0c74ba08ce66" />
## Hình sơ đồ
<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/23a0f1ed-4ddb-44ae-9da9-0008c368b9ac" />







