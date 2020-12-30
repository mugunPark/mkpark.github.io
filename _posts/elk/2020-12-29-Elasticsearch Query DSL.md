---
title: "Elasticsearch Query DSL"
classes: wide
excerpt: "Elasticsearch Query DSL"
header:
  overlay_image: /assets/images/develop-unsplash.jpg
  overlay_filter: rgba(255, 255, 255, 0.3)
categories:
  - elk
  - Elasticsearch
tags:
  - Elasticsearch

last_modified_at: 2020-12-29T13:00:00
---

# Full Text Query

* **interval query** - 일치하는 용어의 순서 및 근접도(proximity)를 세밀하게 제어 할 수 있는 쿼리.

* **match query** - fuzzy matching 및 구문 또는 근접도(proximity) 쿼리를 포함하여 full text query를 실행하기위한 표준 쿼리.

* **match_bool_prefix query** - 입력을 분석하고 각 term으로 bool 쿼리를 구성합니다. 마지막을 제외한 term은 term 쿼리에 사용되고 마지막 term은 prefix 쿼리에 사용합니다.

* **match_phrase query** - match 쿼리와 비슷하지만 정확한 구문 또는 단어 근접 일치를 시키는데 사용됨. 쿼리에 사용되는 slop 옵션은 값만큼 다른 term 사이에 들어갈 수 있는 단어의 수를 설정합니다.

* **match_phrase_prefix query** - match_phrase 쿼리와 비슷하지만 마지막 단어는 와일드 카드로 검색됩니다.

* **multi_match query** - match 쿼리를 다중 필드를 대상으로 할 수 있습니다.

* **query_string query** - Lucene 쿼리 문자열 구문을 지원하여 단일 쿼리 문자열 내에서 AND , OR , NOT 조건 및 다중 필드 검색을 지정할 수 있습니다. 전문가 전용입니다.

# Boolean query

* **must** - 일치하는 document를 나타내며 점수에 기여합니다.

* **filter** - 일치하는 document를 나타내지만 must와 달리 점수는 무시합니다. must 보다 검색 속도가 빠르고 캐싱이 가능합니다.

* **should** - 검색 결과 중 이 쿼리에 해당하는 document의 점수를 높입니다. 

* **must_not** - 일치하지 않는 document를 나타내며, 스코어링이 무시되고 캐싱이 고려되는 필터 컨텍스트에서 실행됩니다.


