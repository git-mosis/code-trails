# Evaluating Chunking Strategies: A Unified Review

## Introduction

Breaking a long document into digestible pieces is a key step in building a question answering system.  Different chunking and retrieval strategies produce very different outputs.  To see how these differences play out in practice, I ran someone's thesis through three pipelines and saved the results to text files: `cosine-w-topk.txt`, `mmr-w-hnsw-output.txt` and `Hybrid-with-Reranking.txt`.  Each file name corresponds to the code used to generate it.  

Below we'll compare the chunking outputs and draw a single conclusion. With the `cosine-w-topk.txt` and the `mmr-w-hnsw-output.txt` implementation, I used the `sentence-transformers/all-MiniLM-L6-v2` embedding model. The `Hybrid-with-Reranking.txt` however uses a hybrid of [BM25](https://zilliz.com/learn/mastering-bm25-a-deep-dive-into-the-algorithm-and-application-in-milvus), a ml algorithm and `intfloat/e5-base-v2` embedding model. 

## Cosine baseline (`cosine-w-topk.txt`)

The cosine baseline splits the document into fixed 500 character windows with a 100 character overlap and retrieves the top one thousand nearest neighbours by cosine similarity.  The output file shows how this naive approach scatters the text.  The first few chunks illustrate the problem:

> **Chunk 1** – may be able to see if there are any significant changes in the categories over time as well as which
>
> **Chunk 2** – Economic Sciences. Retrieved from https://www.nsf.gov/statistics/2017/nsf17310/static/downloads/nsf17310-digest.pdf National Student Clearinghouse Research Center. (2017). Undergraduate degree earners report …
>
> **Chunk 3** – 2. Implicit Computer Science
> 
> .18
> 
> Identity
> 
> (24)
> 
>
> .02
> 
> .35
> 
> Identity Imbalance

These fragments contain partial sentences, headings and even a list of references.  Important content is buried under tables of contents, numbers and citation lines.  Readers must piece together meaning from multiple unrelated snippets.

## HNSW with maximal marginal relevance (`mmr-w-hnsw-output.txt`)

The HNSW method uses the same fixed window chunking but stores embeddings in a hierarchical navigable small world index and applies maximal marginal relevance to select a diverse set.  My friend shared this technique with me and wanted to take it for a spin. The output is more varied than the cosine baseline, but it still suffers from the underlying splitting.  Examples from the file:

> **Chunk 1** – Economic Sciences. Retrieved from … National Student Clearinghouse Research Center. (2017). Undergraduate degree earners report … Nosek, B. A., Greenwald, A. G., & Banaji, M. R. (2005). Understanding and using the implicit association
>
> **Chunk 2** – Testing Greenwald and colleagues’ BID model resulted in significant regressions, suggesting that the IAT and the BID can be used as methods to predict implicit identities.  Additionally, due to the BID providing positive results, we are confident in the measures of imbalance …
>
> **Chunk 3** – second step for this model also accounted for four per cent of the variance.  Similar to the previous model, science identity and science stereotype were not significant predictors of ethnic identity …

These passages contain more substantive information, including statistical results and discussion of implicit identity tests.  However, they still begin and end abruptly, and stray citations and headings are interspersed within the content.

## Hybrid retrieval with reranking (`Hybrid-with-Reranking.txt`)

The hybrid pipeline performs structure aware chunking, deduplicates near duplicates and combines dense and sparse retrieval before reranking the results.  Its output file demonstrates how respecting paragraph boundaries and reranking improves readability.  The top passages include:

> **Chunk 1** – Greenwald and colleagues (1998) outlined a five item per category test and a twenty five item per category test.  These shorter IATs were selected to account for time.  The computer science identity IAT measured the level of association between self and computer science … Positive scores reflect stronger associations toward a computer science identity; negative scores reflect negative associations between self and computer science such that the individual does not identify as a computer scientist.  The implicit gender identity test measured how strongly the person associates themselves toward the stated gender.
>
> **Chunk 2** – Preliminary analyses for mid survey data were conducted to view distributions and descriptive statistics of the variables.  Histograms revealed that interest in taking computer science classes had a slight negative skew, interest in a career in computer science had a positive skew, happiness was normally distributed, implicit computer science identity was slightly negatively skewed and identity imbalance was highly skewed, so logarithmic transformations were conducted.
>
> **Chunk 3** – College participants were asked what career they were in and if it was science related.  These survey data were compared with additional data requested from the National Student Clearing House.  The final analytic sample included one hundred seventy participants who completed the survey items and three IATs measuring ethnic identity, science identity and the ethnic science stereotype.  Relationships between these scores were needed to assess identity balance.

Each hybrid chunk reads like a complete paragraph.  They provide context, definitions, methods, and results without stray headings or citations.  This makes the information easy to digest and directly relevant to the question.

## Conclusion

The comparative outputs make one conclusion clear: **structure aware chunking with hybrid retrieval and reranking yields the most coherent and informative results**.  The cosine baseline and the HNSW with MMR method rely on small fixed windows; their outputs are noisy and fragmented, filled with citations and partial sentences.  The hybrid method respects the document’s structure, removes duplicates and reranks for relevance, producing concise passages that focus on the core methods and findings of the thesis.  For anyone building a retrieval augmented question answering system, choosing a sophisticated chunking and reranking pipeline is essential for clarity and accuracy.
