# Agentar-Scale-SQL: Advancing Text-to-SQL through Orchestrated Test-Time Scaling

<div align="center">

[![Paper](https://img.shields.io/badge/paper-arXiv-red)](https://arxiv.org/abs/2509.24403)
[![Leaderboard](https://img.shields.io/badge/BIRD%20Leaderboard-%231-brightgreen)](https://bird-bench.github.io/)
[![Hugging Face](https://img.shields.io/badge/%F0%9F%A4%97%20Hugging%20Face-Models-yellow)](https://huggingface.co/collections/antgroup/agentar-scale-sql)
[![ModelScope](https://img.shields.io/badge/ModelScope-Models-blue)](https://modelscope.cn/collections/Agentar-Scale-SQL-0c368e98f73f41)

</div>

## 📝 Introduction
**Agentar-Scale-SQL** is a novel framework that leverages scalable computation to significantly improve Text-to-SQL performance on challenging benchmarks. By implementing an Orchestrated Test-Time Scaling strategy, our framework synergistically combines three distinct perspectives to bridge the gap between state-of-the-art models and human expert performance.

<figure>
  <img src="./resources/framework.png" 
       alt="framework" 
       width="1200" 
       style="display: block; margin: 0 auto;">
  <figcaption style="text-align: center; font-style: italic; color: #555;">
    Figure 1:  The proposed Agentar-Scale-SQL framework.
  </figcaption>
</figure>

---

## ⚡️ Performance

| Methods                      | EX (Dev) | **EX (Test)** | R-VES (%) |
|:-----------------------------|:---:|:---:|:---------:|
| **Agentar-Scale-SQL (Ours)** | **74.90** | **81.67** | **77.00** |
| AskData + GPT-4o             | 76.14 | 80.88 |   76.24   |
| LongData-SQL                 | 74.32 | 77.53 |   71.89   |
| CHASE-SQL + Gemini           | 74.90 | 76.02 |   69.94   |
| JoyDataAgent-SQL             | 74.25 | 75.85 |   70.16   |
| TCDataAgent-SQL              | 74.12 | 75.74 |     -     |
| Contextual-SQL               | 73.50 | 75.63 |   70.02   |
| XiYan-SQL                    | 73.34 | 75.63 |   71.41   |

---

## 🎉 News
- 🎁 2025.09.30: Our paper is available on [arXiv](https://arxiv.org/abs/2509.24403).
- 🎁 2025.09.25: 🏆 We have achieved **#1 Rank** on the official [BIRD leaderboard](https://bird-bench.github.io/) with **81.67%** execution accuracy!

---

## 🗺️ Release Roadmap

We are committed to continuously improving **Agentar-Scale-SQL**. Here is our plan for upcoming features and releases.

-   **Paper**
    -   `[x]` Publish the paper on arXiv.
-   **Model Releases**
    -   `[x]` Release **Agentar-Scale-SQL-Generation-32B** on Hugging Face and ModelScope.
    -   `[ ]` Release **Agentar-Scale-SQL-Selection-32B** on Hugging Face and ModelScope.
-   **Code Releases**
    -   `[x]` Release the code for the light schema engine.
    -   `[x]` Release the code for the offline data preprocessing pipeline.
    -   `[ ]` Release the code for task understanding and generating SQL candidates with closed-source models.
    -   `[ ]` Release the code for generating SQL candidates with the fine-tuned model.
    -   `[ ]` Release the code for the SQL selection module.

---

## 📂 Directory Structure

```bash
Agentar-Scale-SQL/
├── ScaleSQL/                     # Core source code directory
│   └── workflows/                # Main workflow scripts
│       └── config/               # Configuration files
├── ddl_schema.sh
├── requirements.txt              # Dependency list
├── .env                          # Environment variable
├── .env.example                  # Environment variable template
├── .gitignore
├── README.md                     # Current document
├── nltk_data.zip                 # For ddl schema generation
```

---

## 📚 Usage

### 1. Installation and Environment Settings

#### 1.1 Create Virtual Environment and Install Python Dependencies

```bash
conda create -n ScaleSQL python=3.10
conda activate ScaleSQL
```

---

#### 1.2 Install PyTorch and Core Dependencies

```bash
# Install PyTorch (CUDA 12.1)
pip install torch==2.5.1 torchvision==0.20.1 torchaudio==2.5.1 --index-url https://download.pytorch.org/whl/cu121
```

---

#### 1.3 Install Project Dependencies

```bash
pip install -r requirements.txt
```

---

#### 1.4 Install vLLM (for Inference Acceleration)

```bash
pip install https://github.com/vllm-project/vllm/releases/download/v0.8.5.post1/vllm-0.8.5.post1+cu121-cp38-abi3-manylinux1_x86_64.whl
```

---

#### 1.5 Download Embedding Model

```bash
modelscope download --model sentence-transformers/all-MiniLM-L6-v2 --local_dir ./ScaleSQL/model/all-MiniLM-L6-v2
```

---

### 2. Data Preparation

#### 2.1 Configure Paths

Modify the configuration file: `.ScaleSQL/workflows/config/pipeline_config.yaml`.
Note that, we need column meaning file in the evaluation.

```yaml
dataset_folder: /temp/bird_test  # Change to the actual folder
column_meaning_path: /your_path/column_meaning.json # Change to the actual path
```

---

### 3. Preprocessing Pipeline

---

#### 3.1 Generate Light Schema

```bash
python -m ScaleSQL.workflows.schema_generation --evaluation_type test
```

> Output example: `.ScaleSQL/dataset/bird_test_light_schema.json`

---

#### 3.2 Process Training Set Examples and Write to Vector Database

```bash
ANONYMIZED_TELEMETRY=False python -m ScaleSQL.workflows.train_skeleton_process
```

> Output path: `/tmp/ScaleSQL/chroma/bird_train_skeleton`

---

#### 3.3 Process Database Cell Values and Write to Vector Database

```bash
ANONYMIZED_TELEMETRY=False python -m ScaleSQL.workflows.database_cell_process --evaluation_type test
```

> Output path: `/tmp/ScaleSQL/chroma/bird_test`

---

#### 3.4 Build BM25 Index (Content-Based) and Generate DDL Schema (Requires Java Environment)

```bash
bash ddl_schema.sh
```

> Output example: `.ScaleSQL/dataset/bird_test_ddl_schema.json`

---

## 📦 Try Our Product

We are developing a ChatBI product that transforms complex business data into conversational insights. If you are interested in trying our ChatBI product, please contact us.

<figure>
  <img src="./resources/dingding.png" 
       alt="dingding" 
       width="300" 
       style="display: block; margin: 0 auto;">
  <figcaption style="text-align: center; font-style: italic; color: #555;">
    Figure 2:  The contact information.
  </figcaption>
</figure>

---

## 📎 Citation

```bibtex
@misc{wang2025agentarscalesqladvancingtexttosqlorchestrated,
      title={Agentar-Scale-SQL: Advancing Text-to-SQL through Orchestrated Test-Time Scaling}, 
      author={Pengfei Wang and Baolin Sun and Xuemei Dong and Yaxun Dai and Hongwei Yuan and Mengdie Chu and Yingqi Gao and Xiang Qi and Peng Zhang and Ying Yan},
      year={2025},
      eprint={2509.24403},
      archivePrefix={arXiv},
      primaryClass={cs.CL},
      url={https://arxiv.org/abs/2509.24403}, 
}
```

