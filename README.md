# API and NER Tech Test

My solution to the test as described [here](https://gist.github.com/mattmcgrattan/42172e829e50ce037ae62927cb95ecfe).

The code will analyse the text on the URL given in the payload and extract and count named entities. For each person, it will count the occurrences and compute which locations are mentioned within a span of 100 words to both sides of the name. 

Output example:

```
{
  "URL": "https://raw.githubusercontent.com/athrado/api_task/main/sample_text.txt",
  "title": "Travel Stories",
  "author": "ChatGPT",
  "people": [
    {
      "name": "Sarah",
      "count": 5,
      "assosciated_places": [
        {
          "name": "Kyoto",
          "count": 2
        },
        {
          "name": "Barcelona",
          "count": 1
        },
        {
          "name": "Japan",
          "count": 1
        },
    }
  ]
}
```

### Data

The API tool was mostly tested with texts on [Project Gutenberg](https://www.gutenberg.org). For Gutenberg texts the header and footer of the text body will be cut off to analyse only the book content. 

For an easier example, check the [sample text](https://raw.githubusercontent.com/athrado/api_task/main/sample_text.txt) about travel adventures generated by ChatGPT. The text was manually modified to include filler sequences to enable easy counting of locations within a 100 word span. Note that the last section mentions all character names once more. 

### Setup

Install spaCy according to the [documentation](https://spacy.io/usage), for example:

```
pip install -U pip setuptools wheel
pip install -U spacy
python3 -m spacy download en_core_web_sm
```

Then install the remaining requirements:

```pip install -r requirements.txt```

### How to run

For example, you can test the API with the sample text generated by ChatGPT. Which character travelled the most? 

```curl -X POST -H "Content-Type: application/json" -d '{"URL": "https://raw.githubusercontent.com/athrado/api_task/main/sample_text.txt", "author": "ChatGPT", "title": "Travel Stories"}' http://127.0.0.1:8000/get_text/```

After making adjustments to the code, you can test it using: `pytest app/test_main.py`

### How to improve

There are several ways in which the code could be improved. 

- Applying coreference resolution to named entities to ensure mentions of the same entity in different formats are counted accurately, e.g. "Sarah" instead of "Sarah Potter" or "NY" instead of "New York". 

- Re-train or fine-tune a NER tagger for literay texts. Traditionally, NER taggers are trained on news texts so they may not be suitable for literary works. For example, we would want to consider _Manchester_ a location, not an organisation as the spaCy NER tagger suggests. We also want to avoid entities like _the United States_ with an article. 

- Further enrichment of the data, for example by adding coordinates to the locations to facilitate visualisations of the data.

- Speed it up with multiprocessing of overlapping paragraphs (to keep span information) for longer texts.

- Have code reviewed and test API in real-world application. Then optimise based on performance and user feedback.  

