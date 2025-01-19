**Research Proposal: Automated Generation of Financial Alpha Signals Using a Multi-Encoder Transformer Model**

**1. Introduction**

*   **Problem Statement:** Generating profitable trading signals (alphas) in financial markets is a complex and challenging task. Traditional methods often struggle with the semantic nuances of financial text and the combinatorial nature of alpha construction, and often require significant manual feature engineering and domain expertise, which is difficult to scale.
*   **Proposed Solution:** We propose an automated system that leverages a novel multi-encoder transformer architecture to generate viable alpha expressions. This model will simultaneously learn the structure of alpha expressions, the rich semantics of financial text, and the relationship between them, providing a more robust, flexible, and effective approach to alpha generation. Our approach will leverage domain adaptation using a pre-trained model fine-tuned on financial text.

**2. Literature Review**

*   **Background:**
    *   Quantitative trading and the importance of alpha signals for portfolio construction and trading strategies.
    *   The challenges of manual alpha development, including reliance on domain expertise, inefficient feature engineering processes, and lack of scalability.
    *   Limitations of traditional machine learning approaches in handling complex unstructured data such as text.
    *   The advent of deep learning in NLP, with a particular focus on the success of transformer models.
    *   The importance of transfer learning, where pretrained models provide a solid starting point for NLP tasks.
    *   The need for domain adaptation to ensure pre-trained models are suitable for domain-specific tasks.
*   **Relevant Research:**
    *   "101 Formulaic Alphas" ([Kakushadze and Tulchinsky, 2015]), which serves as a source of real-world alpha examples and a good benchmark for evaluating our own work.
    *   FinBERT and similar domain-specific language models for financial NLP ([Araci, 2019]), which have proven the need for financial text specific pre-training and fine-tuning. This model and related work have shown the effectiveness of using pretrained transformer models to enhance performance of NLP tasks in the financial domain.
    *   Multi-encoder architectures and methods for combining their outputs, including concatenation ([Devlin, 2018]), attention mechanisms (cross-attention) ([Vaswani, 2017]), and fusion layers ([Ying, 2021]).
    *   Techniques for training deep learning models using dynamic learning rates (e.g., slanted triangular learning rates), discriminative fine-tuning and gradual unfreezing to avoid catastrophic forgetting ([Howard, 2018]).
*   **Knowledge Gap:**
    *   Limited prior research directly addresses automated *generation* of alpha expressions, with most previous work focused on classification of text or other data.
    *   Existing models often lack a robust approach to integrate both the structured format of alpha expressions with unstructured data sources like financial text.
    *   Research does not address the best way to leverage multi-encoders for alpha generation.

**3. Proposed Methodology**

*   **Overall Approach:** We will build a multi-encoder transformer model that processes both alpha expressions and financial documents in a coordinated manner. This model is designed to generate new alpha expressions that are not only syntactically correct but are also more likely to be financially relevant by leveraging financial documents as a context. We will also implement an evaluation system to filter out poor performing alpha expressions, resulting in a more robust system that outputs higher quality alpha signals.
*   **Alpha Expression Format and Structure:**
    *   **Expression Language:** Alpha expressions will follow a specific grammar with the following elements:
        *   **Data Fields:** Identifiers for financial metrics such as `open`, `close`, `high`, `low`, `volume`, `vwap`, `cap`, `adv{d}`, and others from our financial datasets and any user defined custom fields.
        *   **Operators:** Arithmetic operators (`+`, `-`, `*`, `/`, `^`), logical operators (`<`, `<=`, `>`, `>=`, `==`, `!=`, `||`, `cond ? expr1 : expr2`), time-series operators (`delay`, `correlation`, `covariance`, `ts_rank`, `sum`, `product`, `stddev`, `decay_linear`, `ts_min`, `ts_max`, `ts_argmin`, `ts_argmax`), cross-sectional operators (`rank`, `indneutralize`), and special operators (e.g. `sign`, `scale`, `abs`, `log`, `signedpower`, `vec_avg`, `ts_skewness`, `group_rank`).
        *   **Numeric Literals:** Integer and floating-point numbers (e.g., `1`, `2.5`, `-10`, `.001`).
        *   **Grouping:** Parentheses `(` and `)` for controlling order of operations.
        *   **Lists:** Comma separated arguments for functions requiring multiple arguments (e.g. `correlation(open, close, 10)`).
    *   **Expression Examples:**
        *   **Simple:** `close`, `nws17_d1_nip`
        *   **Arithmetic:** `fn_assets_fair_val_l1_q / fn_liab_fair_val_a`, `(high + low) / 2`
        *   **Conditional:** `cond ? nws17_d1_qcm > 0.5 : 0`
        *   **Time Series:** `delta(close, 1)`, `rank(decay_linear(close, 10), 5)`, `correlation(adv(20), low, 12)`, `ts_min(high, 5)`
        *   **Cross Sectional:** `rank(rsk59_short_interest)`, `indneutralize(close, sector)`
        *  **Complex:** `((rank(rank(rank(decay_linear((-1 * rank(rank(delta(close, 10)))), 10)))) + rank((-1 * delta(close, 3)))) + sign(scale(correlation(adv(20), low, 12))))`, `((close - open) / ((high - low) + .001))` ,  `group_rank(ts_skewness(vec_avg(nws17_d1_nip),120),sector)`, `ts_skewness(vec_avg(nws17_d1_nip),120)`
    *   **Data Type Handling:** We will use a data abstraction layer to convert data field tokens to their corresponding numerical values.
    *   **Valid Expressions:** We will enforce the generation of valid expressions by creating a tokenizer that is aware of our expression grammar, including proper matching of parentheses and number of arguments for each operator. We will also enforce certain semantic rules when generating expressions to avoid illogical combinations, such as only using numbers as arguments for time-based shifts.
*   **Dual Encoder System:**
    *   **Alpha Expression Encoder:** This encoder will be based on TinyBERT (or a similar transformer model). It will be trained to generate a latent space representation of alpha expressions.
    *   **Financial Document Encoder:** This encoder will be based on a pre-trained financial language model, either FinBERT, or a smaller version such as DistilBERT that has been fine tuned on a large financial text corpus. It will generate a latent space representation of the financial documents that act as context for the alpha expressions.
*   **Fusion Mechanism:**
    *   The encoded alpha expressions and the encoded financial text representation will be combined using one of the following strategies:
        *   **Concatenation:** The vector outputs of each of the encoders are combined by appending them together.
        *   **Cross-Attention:** An attention mechanism where the query is from the alpha encoder output, the key and value are from the financial encoder output.
        *   **Fusion Layer:** A neural network layer that is trained to combine the two encoder outputs in a non-linear way.
*   **Decoder:**
    *   The decoder will use the combined encoder outputs to generate new, valid alpha expressions. The decoder will use both self-attention to attend to the previously generated alpha expression tokens, and cross-attention to attend to both the encoded alpha expression and financial text embeddings. This allows the decoder to make use of the information from both encoders to generate better output.
*   **Evaluation System (ACE):**
    *   The evaluation system will use the output of our multi-encoder model to generate an alpha expression and then evaluate the quality and profitability of that alpha using real market data. The system will perform the following tasks:
        *   **Data Lookup:** Retrieve numerical values based on the alpha expression and data fields from historical market datasets.
        *   **Operator Evaluation:** Implement a module that parses the expression into an evaluation tree and processes it using the time series, cross-sectional, logical, special and other operations.
        *   **Simulation:** Evaluate the alpha signal over a time window to produce backtesting metrics.
        *   **Submission Testing:** Implement testing criteria to ensure the output from the system can be considered a viable alpha before passing it on for further use. This involves checking measures such as the Sharpe ratio of the simulated output, which must have a certain minimal threshold.
        *   **Alpha ID Generation:** Generate a unique identifier for each alpha that is generated and evaluated.
        *   **Metadata Collection:** Collect metadata for each alpha including all parameters used for simulation.
        *   **Performance Reporting:** Provide reports on the performance of the alpha expressions (Sharpe ratio, returns, etc.).
        *   **Data Storage:** Persist alpha, results and metadata for future use.
*   **Training:**
    *   **Two Stage Training:**
        *  **General Training (Stage 1):** Train TinyBERT or similar encoders to understand the general language of financial expressions and other NLP tasks. This stage will use a large dataset of synthetically generated and real alpha expressions to learn to encode the grammar and semantics of valid alpha expressions without the use of labels.
        *   **Task Specific Training (Stage 2):** Fine-tune the entire system by using the simulated output from the ACE as a label. The encoders and decoder will be updated using the losses measured from the simulated target and by checking if the alpha is valid.
    *   **Training Data:** The model will be trained on a mix of human-provided, existing, mutated/recombined, and synthetically generated alpha expressions and their corresponding financial documents. A large dataset of general and financial text will be used for pre-training of language models.
    *   **Training Strategy:** We will implement the training strategies outlined in the FinBERT paper, specifically Slanted Triangular Learning Rates, Discriminative Fine-tuning and Gradual Unfreezing.
*   **TinyBERT Training Input Format:**
    *   **Tokenization:** Input alpha expressions will be tokenized using a custom tokenizer. This tokenizer will map each data field, operator, and number to a unique integer ID, maintaining the proper structure using parentheses and lists.
    *   **Input Sequence:** The tokenized expression will be formatted as a sequence of token IDs that are padded or truncated to form equal length batches for processing via our transformer model.
*   **Financial Document Training Input Format:**
     *   **Tokenization:** Financial documents will be tokenized using a tokenizer compatible with the financial document encoder.
    *   **Input Sequence:** The tokenized text will be formatted as a sequence of token IDs to be processed by our transformer model.
    *   **Batching:** The documents will be padded or truncated to form batches of equal length for training purposes.

**4. Implementation Plan (Two Independent Tracks)**

*   **Track 1: FinBERT (or Similar Financial Language Model) Implementation**
    1.  Select a pre-trained financial language model (e.g., FinBERT, a smaller DistilBERT-based model finetuned on a large financial corpus, or similar).
    2.  Fine-tune the model in a suitable environment (e.g., a cloud-based GPU instance or a dedicated machine) using a large financial dataset and the previously described training strategies such as slanted learning rates, discriminative fine-tuning and gradual unfreezing.
    3. Implement a robust API for extracting text embeddings.
    4. Save model weights in a format that can be read by our primary Colab environment for subsequent transfer learning.
*   **Track 2: TinyBERT (and Multi-Encoder) Implementation**
    1.  Design the multi-encoder architecture based on TinyBERT and integrate it with financial document encoder.
    2.  Implement the multi-encoder model with cross-attention, a fusion layer, or similar techniques for combining the encoder outputs.
    3.  Build a custom tokenizer for alpha expressions that can handle all the required syntax, including lists and nesting.
    4.  Implement the API for interacting with the ACE. This involves API calls to receive data, generate alphas, run simulations and get back simulation data.
    5. Implement all operators described in "101 Formulaic Alphas", and other operators, in the evaluation engine, ensuring correct logic for time-series, cross-sectional, logical, special, arithmetic and other operators.
    6.  Implement the training loop for the multi-encoder model using both unlabeled and labelled data.
    7.  Combine all components into a complete system.
* **Testing and Evaluation**
    1. Train and fine-tune the model using the procedures described earlier, including the two stage training strategy, the training data and the training strategies outlined by FinBERT.
    2.  Create a validation system to ensure all components of the framework are working as expected. The system will use validation datasets that have been curated separately from the training sets to avoid bias.
    3. Use the evaluation metrics from "101 Formulaic Alphas" to measure performance of the generated alpha signals and compare the result to those described in the paper, this would include Sharpe ratio, turnover, and other simulation metrics.

**5. Expected Outcomes**

*   A novel system capable of automatically generating potentially viable financial alpha signals, by learning to combine the structure of alpha expressions and the information contained in financial documents.
*   A robust framework leveraging deep learning, specifically transformer architectures, for alpha generation.
*   An evaluation system that simulates and assesses the performance of generated alpha expressions using real market data.
*   A research study analyzing the performance of generated alpha signals and comparing the results with baseline performance.
*   A public repository that can be used for further research.

**6. Timeline**

*   **Phase 1 (Month 1-2):** FinBERT Training and Implementation of Evaluation and Simulation system (separate Google Colab). This involves training a financial language model using existing data as well as implementing an ACE-like evaluation and simulation system that is needed by our primary Colab session.
*   **Phase 2 (Month 2-3):** Multi-Encoder Architecture, Data Preparation and TinyBERT Training (primary Google Colab). This involves designing our multi-encoder system and training the base TinyBERT model, including the tokenizer and implementing all operators for the evaluation engine.
*   **Phase 3 (Month 3-4):** Fine-Tuning, Experimentation, and Initial Evaluation (primary Google Colab). This phase involves fine-tuning the entire multi-encoder system, and performing an initial assessment of the alpha signals.
*   **Phase 4 (Month 4-5):** Comprehensive evaluation of different model configurations, documentation, and write-up. This will involve detailed testing, and evaluation and a report that summarizes all the work that has been performed.

**7. Resources Required**

*   Google Colab (Free tier) and optionally cloud-based GPUs (e.g., Google Cloud Platform).
*   Access to financial market data (can be from a public dataset).
*   Software tools (Python, PyTorch, Transformers library, custom libraries).
*   Storage for training data and persisted model weights.

**8. Potential Risks**

*   Resource limitations within the Google Colab environment (memory and GPU) may require us to use smaller datasets or models.
*   The complexity of implementing the multi-encoder architecture may cause unforeseen delays.
*   Limited availability of labelled data for alpha signals could reduce the training performance.
*   The generated expressions may not generalize well to unseen data.
*   The quality of real market data may be variable and may require additional preprocessing steps.

**Conclusion**

This research proposes a novel and potentially highly effective way to automatically generate high quality trading signals by using a multi-encoder system trained on alpha expression structure and financial text semantics. By combining a domain-adapted language model with a specialized encoder and decoder, we can produce more meaningful and viable alpha signals. We believe this approach has the potential to significantly advance the field of quantitative finance and improve our ability to create robust and reliable trading strategies.

**References**

* [Research Proposal: Automated Alpha Signal Generation using a Transformer Architecture and the Alpha Creation Environment (ACE) II](https://simplexityware.github.io/AlphaSignalGenerationACE2)
* [tokenizer alpha signal expression as domain specific language](https://storm.genie.stanford.edu/article/tokenizer-alpha-signal-expression-as-domain-specific-language-587693)
* [All you need to know about Tokenization in LLMs](https://medium.com/thedeephub/all-you-need-to-know-about-tokenization-in-llms-7a801302cf54)
* [ModernBERT](https://github.com/AnswerDotAI/ModernBERT)
* [FinText](https://huggingface.co/FinText)
* [TinyBERT](https://github.com/huawei-noah/Pretrained-Language-Model/tree/master/TinyBERT)
