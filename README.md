# DM2023-Lab2-master
ISA5810 Data Mining Lab 2


The entire training process is divided into two parts: data preprocessing and model training.

### Data preprocessing
- Work for me:
    1. Remove html, 
    2. Remove metion, 
    3. Split connective (i.e. HowAreYou -> How are you), 
    4. Lower case transformation
    5. Contractions

``` python
class MyPreProcessor:
    '''
    Main process pipeline is in __call__ function, so you can jump into __call__ funciton to see the detail
    '''
    def __init__(self) -> None:
        self.lemmatizer = WordNetLemmatizer()

    def lemma_traincorpus(self, text):
        words = word_tokenize(text)
        lemmatized_words = [self.lemmatizer.lemmatize(word) for word in words]

        return ' '.join(lemmatized_words)

    def contractions(self, text):
        abbreviations = {
            "aren't" : "are not",
            "can't" : "cannot",
            "couldn't" : "could not",
            ...
            ...
            Write from scratch, so you can check at Homework/DM2023-Lab2-Homework.ipynb for more details
            ...
            "shouldnt" : "should not",
            "werent" : "were not",
            "gonna" : "going to",
            "imma" : "i am going to"
        }

        for key, value in abbreviations.items():
            text = re.sub(re.escape(key), value, text)
        return text
    
    def rm_space(self, text):
        text = text.strip()
        text = text.split()
        return ' '.join(text)

    def clean(self, text):
        text = re.sub(r"&gt;", ">", text)
        text = re.sub(r"&lt;", "<", text)
        text = re.sub(r"&amp;", "&", text)
        text = re.sub(r'&[^ ]*', '', text)
        
        return text

    def __call__(self, text):
        # rm html
        html = re.compile(r'<.*?>')
        text = html.sub(r'',text)

        # rm URL
        url = re.compile(r'https?://\S+|www\.\S+')
        text = url.sub(r'',text)

        # rm informal space
        text = self.rm_space(text)

        # clean
        text = self.clean(text)

        # rm metion
        text = re.sub(r'@\S+', '', text)

        # Split connective
        text = re.sub(r'([a-z])([A-Z])', r'\1 \2', text)

        # lower case transformation
        text = text.lower()

        # contractions
        text = self.contractions(text)

        return text

```
- Not work for me:
    1. Remove emoji
    2. Spell correction (Jamspell)

- TODO
    1. NER


### Model training
Ensemble (Majority vote) from
1. bert-base-uncased
2. roberta-base
3. distilroberta-base

### Result
| Rank        | Feature Engineer        | Model              | Score (private) |   Score (public)    |
|-------------------------|-------------------------|--------------------|--------------------------|--------------------|
|-| Rule based              | bert-base-uncased |              0.544          |      0.560        |
|-| Rule based              | roberta            |            0.547            |     0.561          |
|-| Rule based              | distill roberta   |             0.536           |       0.552         |
|-| Rule from ekphrasis     | bert-base-uncased |              0.487           |      0.501       |
|6/99| Rule based + Majority vote | bert-base-uncased/roberta/distill roberta |       0.552         | 0.568               |
|-| Rule from ekphrasis + Majority vote | bert-base-uncased/roberta/distill roberta | 0.550      | 0.567                |
