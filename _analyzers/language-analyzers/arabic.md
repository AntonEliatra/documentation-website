---
layout: default
title: Arabic
parent: Language analyzers
grand_parent: Analyzers
nav_order: 10
---

# Arabic analyzer

The built-in `arabic` analyzer can be applied to a text field using the following command:

```json
PUT /arabic-index
{
  "mappings": {
    "properties": {
      "content": {
        "type": "text",
        "analyzer": "arabic"
      }
    }
  }
}
```
{% include copy-curl.html %}

You can also use `stem_exclusion` with any language analyzer using the following command:

```json
PUT index_with_stem_exclusion_english_analyzer
{
  "settings": {
    "analysis": {
      "analyzer": {
        "stem_exclusion_english_analyzer":{
          "type":"arabic",
          "stem_exclusion":["authority","authorization"]
        }
      }
    }
  }
}
```
{% include copy-curl.html %}

## Arabic analyzer internals

The `arabic` analyzer is build using the following:

Tokenizer: `standard`

Token Filters:
- decimal_digit (general)
- stop (arabic)
- normalization (arabic)
- keywords (arabic)
- stemmer (arabic)

## Custom Arabic analyzer

You can create custom Arabic analyzer using the following command:

```json
PUT /arabic-index
{
  "settings": {
    "analysis": {
      "filter": {
        "arabic_stop": {
          "type": "stop",
          "stopwords": "_arabic_"
        },
        "arabic_stemmer": {
          "type": "stemmer",
          "language": "arabic"
        },
        "arabic_normalization": {
          "type": "arabic_normalization"
        },
        "decimal_digit": {
          "type": "decimal_digit"
        }
      },
      "analyzer": {
        "arabic_analyzer": {
          "type": "custom",
          "tokenizer": "standard",
          "filter": [
            "lowercase",
            "arabic_normalization",
            "decimal_digit",
            "arabic_stop",
            "arabic_stemmer"
          ]
        }
      }
    }
  },
  "mappings": {
    "properties": {
      "content": {
        "type": "text",
        "analyzer": "arabic_analyzer"
      }
    }
  }
}
```
{% include copy-curl.html %}

## Generated tokens

Use the following request to examine the tokens generated using the analyzer:

```json
POST /arabic-index/_analyze
{
  "field": "content",
  "text": "الطلاب يدرسون في الجامعات العربية. أرقامهم ١٢٣٤٥٦."
}
```
{% include copy-curl.html %}

The response contains the generated tokens:

```json
{
  "tokens": [
    {
      "token": "طلاب",
      "start_offset": 0,
      "end_offset": 6,
      "type": "<ALPHANUM>",
      "position": 0
    },
    {
      "token": "يدرس",
      "start_offset": 7,
      "end_offset": 13,
      "type": "<ALPHANUM>",
      "position": 1
    },
    {
      "token": "جامع",
      "start_offset": 17,
      "end_offset": 25,
      "type": "<ALPHANUM>",
      "position": 3
    },
    {
      "token": "عرب",
      "start_offset": 26,
      "end_offset": 33,
      "type": "<ALPHANUM>",
      "position": 4
    },
    {
      "token": "ارقامهم",
      "start_offset": 35,
      "end_offset": 42,
      "type": "<ALPHANUM>",
      "position": 5
    },
    {
      "token": "123456",
      "start_offset": 43,
      "end_offset": 49,
      "type": "<NUM>",
      "position": 6
    }
  ]
}
```