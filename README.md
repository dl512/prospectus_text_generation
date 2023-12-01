# prospectus_text_generation
Try to generate some text using N-Grams

Introduction

Prospectuses, especially those in the Hong Kong stock market, are highly standardized texts with a pre-defined structure. For example, there will always be a section about “industry”, following by “business”, and “financial information”. The paragraphs describing the company itself are tailor-made, but creativity is limited. Many companies start with statements like “we are the largest … by [some metrics] according to [someone]”, for example.

Nowadays, ChatGPT can generate a good draft of a prospectus in seconds. However, as I am currently studying NLP, I am curious if I can also generate sensible texts using a more traditional approach — N-Gram.

N-Gram is easy to explain. Essentially, given “N” words, you predict the next one after analyzing a large volume of text. The parameter N determines how many words you consider.

Data Collection

I have downloaded the prospectuses of all HK IPOs in 2023, amounting to a total of 59. To make things simple, I only used the “Summary” section, which typically contains 5–10 pages of information about the company’s business.

There are a total of 18,000 sentences, and 15,000 “words” (including punctuation, numbers, and other random symbols).

Data Processing

Since the file is in PDF format, I am using the PyPDF2 library to convert the information into text.

However, there are two problems. First, the header and footer are included, which are not of interest to me. Specifically, at the top of every page, there is a warning sentence that looks like this “THIS DOCUMENT IS IN DRAFT FORM, INCOMPLETE AND SUBJECT TO CHANGE AND THAT…”. Second, the word extraction process may introduce some typos, such as “anddevelopment” which obviously consists of two words.

Since the second problem is relatively rare, I will simply ignore it. As for the first issue, I have taken a “shortcut” by excluding all words that are all-capitalized. I believe the resulting processed data is satisfactory.

N-Gram Counts

Counting N-Grams is straight-forward. One thing to note is that in order to generate the first and the last words, a dummy word "s" or "e" is added to the beginning and the end of the sentence, respectively. Additionally, some low-frequency words will be converted into an <unk> token to account for unexpected words. You can check out any tutorial for more detailed explanation.

So, here are the 5 most frequent N words (excluding "s", "e", symbols, and the low-frequency words):

N=1: “the”, “of”, “and”, “to”, “in”
N=2: “of the”, “of our”, “for the”, “in the”, “to the”
There is nothing interesting for N=1 and N=2. All of these are simply the most common English words.

N=3: “track record period”, “the track record”, “during the track”, “for the year:, “the year ended”
N=4: “the track record period”, “during the track record”, “for the year ended”, “the latest practicable date”, “the six months ended”
N=5: “during the track record period”, “for the year ended December”, “of the latest practicable date”, “as of the latest practicable”, “the following table sets forth”
However, for N=3, 4, and 5, we observed some common terms that specifically reflect the structure of a Hong Kong IPO prospectus. Terms such as “track record period” and “latest practicable date” repeatedly appear in the text.

Fill in the blank

Although the size of the text is not huge, it is good enough to estimate the next word for some common terms. For example, “according” → “to,” “net” → “profit,” “business” → “model.”

Text generation

This is the most interesting part. The mechanism is to generate the word with the highest probability based on the previous N words. One problem arises when the previous N words have never appeared in the database. In such cases, the model will assign an equal probability to all words in the dictionary. Consequently, either the same word will consistently appear whenever an unknown phrase is encountered, or a completely random word will appear each time. Both scenarios are not ideal.

One solution is that if the previous N words do not exist in the database, we can look up the N-1 words, and so on. Since all the words are originally from the database, the previous word (N=1) will definitely be available.

Here is the outcome:

N=1: we have been no material adverse impact on the [ ] , and the [ ] , and the [ ] , and the [ ] , and the [ ] ,…
Complete gibberish.

N=2: we have been approved by the increase in our revenue from our consolidated statements of profit or loss and other comprehensive income for the year ended december 31 , 2022 , respectively.
It still doesn’t make sense, but it is a significant improvement over random text.

N=3: we have also entered into new sales of 45.5 % from million in 2020 to million in 2021 , and further increased to million as of december 31 , 2020 , 2021 and 2022 , respectively . we have also entered into exclusive –2 , “ ” . we have also built extensive expertise in payload-linkers , which acrossthe as at 30 june 2022 , we had not been subject to any material fines or other material penalties dueto non-compliance with cybersecurity or data privacy or any cybersecurity review from the , the , the , the , the ,
The sentences are (largely) grammatically correct and convey some meaning. However, there are random numbers and symbols in the middle, and towards the end, the list of “the” is strange.

N=4: we have also implemented a number of internal rules and policies to extend the lifecycle of our games ’ ’ . we have also entered into collaboration and license agreements with 21,123 ) ( 6.4 ) ( 34,354 ) ( 3.5 ) ( ofthis document . we have also implemented stringent control on our inventory level and the requirements of our customers . we have also adopted treasury policies to manage our daily expenses and cash withdrawals , so as to ensure our working capital sufficiency by taking advantage of modern technologies to develop servicecategory . we have also established
It is similar to N=3, but it primarily revolves around implementing policies.

Conclusion

I am surprised by how a simple model can generate coherent and meaningful texts. The presence of random symbols and typos is likely influenced by the quality of the source data. Nevertheless, I am genuinely impressed by the generated output.
