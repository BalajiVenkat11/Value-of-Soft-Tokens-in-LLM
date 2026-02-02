# Soft-Token Distillation: Capturing Semantic Richness

This repository explores **soft-token distillation** in Large Language Models (LLMs) and contrasts it with traditional hard-token Supervised Fine-Tuning (SFT).

---

## 📖 Overview

In knowledge distillation, a smaller "student" model aims to replicate the behavior of a larger "teacher" model. The core difference lies in *what* information is passed down:

* **Hard-token SFT:** The student learns only from the **single argmax token** (the "correct" answer) of the teacher.
* **Soft-token Distillation:** The student leverages the **full probability distribution** (or top-K) at each generation step, preserving **semantic richness** and teacher uncertainty.

---

## ⚖️ Hard Tokens vs. Soft Tokens

### Hard Tokens (One-Hot)
Only the highest probability token from the teacher is used as the target.

* **Example:**
    * **Prompt:** `"Romeo and Juliet are ..."`
    * **Hard Target:** `"main"`
* **Pros:** Computationally cheap; uses standard cross-entropy loss.
* **Cons:** Ignores alternative plausible tokens; limits the student's ability to understand nuance.

### Soft Tokens (Distribution)
The teacher provides a probability distribution over the vocabulary. The student learns by minimizing the difference between its distribution and the teacher's, typically using **KL Divergence**:

$$D_{KL}(P_{teacher} \parallel Q_{student})$$

#### Example: Semantic Distributions
Multiple tokens often carry significant probability, revealing the teacher's internal logic.

**Step A:** `"Romeo and Juliet are ..."`
| Token | Probability | Visual |
| :--- | :--- | :--- |
| `main` | 0.270 | ▇▇▇▇▇▇ |
| `protagonists` | 0.263 | ▇▇▇▇▇ |
| `star` | 0.136 | ▇▇ |
| `two` | 0.080 | ▇ |
| `lovers` | 0.048 | ▇ |

**Step B:** `"What is the relationship between Romeo and Juliet?"`
| Token | Probability | Visual |
| :--- | :--- | :--- |
| `tragic` | 0.415 | ▇▇▇▇▇▇▇▇ |
| `play` | 0.374 | ▇▇▇▇▇▇▇ |
| `story` | 0.098 | ▇ |
| `famous` | 0.067 | ▇ |
| `classic` | 0.032 | ▏ |

---

## 🚀 Why Soft Tokens Matter

1.  **Semantic Richness:** Preserves the teacher’s internal knowledge and recognizes that multiple words can be "right."
2.  **Better Generalization:** The student learns the relationships *between* words (e.g., that "protagonists" and "main" are related), leading to better performance on novel prompts.
3.  **Probabilistic Reasoning:** Encodes uncertainty, which is vital for safety, calibration, and downstream reasoning tasks.

---

## ⚙️ Computational Considerations

| Feature | Hard-Token SFT | Soft-Token Distillation |
| :--- | :--- | :--- |
| **Memory** | Low (stores index only) | High (stores probability vectors) |
| **Compute** | Fast (Standard CE) | Slower (KL Div over Top-K) |
| **Robustness** | Moderate | High |

> [!IMPORTANT]
> **The Trade-off:** While soft-token distillation is more resource-intensive, it produces richer, more robust student models that capture the "dark knowledge" of the teacher model.

---

---
