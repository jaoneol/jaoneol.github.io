---
title: '[알쓸엘잡]알아두면 쓸모있는 LLM 잡학사전'
description: LLM 추론 방법 및 성능 비교
author: DS2Man
date: 2025-01-05 11:00:00 +0000
categories:
  - '[알쓸엘잡]알아두면 쓸모있는 LLM 잡학사전'
tags:
  - LLM
  - LangChain
math: true
pin: true
---

LLM을 통해서 추론하는 방법은 **Huggingface Transformers**, **OpenAI API**를 활용하는 방법이 있다. Application을 구축하기 위해 **LangChain**과 **Semantic Kernel** 프레임워크 중에서 LangChain으로 Application을 구축하고자 한다. Huggingface 등록된 모델 중 **polyglot-ko-1.3b**와 **Llama-3.2-3B-Instruct**을 대상으로 스터디해보았다.

#### **1. `LangChain 구성`**
```  
    1. Transformer : 모델과 토크나이저 로드
    2. Pipeline : 파이프 라인 구성
    >>> 여기까지가 기본 LLM 사용법(명시적 접근)과 동일
    3. HuggingFacePipeline : LangChain을 위한 HuggingFacePipeline 객체 생성
    4. PromptTemplate : Prompt 생성
    5. langchain : PromptTemplate 생성
```
~~~python
from langchain.prompts import PromptTemplate
from langchain_huggingface import HuggingFacePipeline
from transformers import AutoTokenizer, AutoModelForCausalLM, pipeline

myModel = "Llama-3.2-3B-Instruct"

if myModel == "Llama-3.2-3B-Instruct":
    model_id = "./Pretrained_byGit/Llama-3.2-3B-Instruct"
else:
    model_id = "./Pretrained_byGit/polyglot-ko-1.3b"

# Step 1: 모델과 토크나이저 로드
tokenizer = AutoTokenizer.from_pretrained(model_id)
model = AutoModelForCausalLM.from_pretrained(model_id, device_map="auto")

if tokenizer.pad_token_id is None:
    tokenizer.pad_token_id = 0  # You can choose 0, 50256, or another token ID
    print(f"Pad token ID is set to: {tokenizer.pad_token_id}")
else:
    print(f"Pad token ID already set: {tokenizer.pad_token_id}")

# Step 2: Pipeline 생성
pipe = pipeline(
    "text-generation",
    model=model,
    tokenizer=tokenizer,
    max_new_tokens=512,
    temperature=0.1,
    pad_token_id=tokenizer.pad_token_id,
)

# Step 3: LangChain을 위한 HuggingFacePipeline 객체 생성(객체 pipe를 wrapping)
llm = HuggingFacePipeline(pipeline=pipe)

# Step 4: PromptTemplate 생성
if myModel == "Llama-3.2-3B-Instruct":    
    template = """<|begin_of_text|><|start_header_id|>system<|end_header_id|>
    당신은 친절한 AI 어시스턴트입니다. 당신의 이름은 TITAN 입니다. 질문에 간단히 답해주세요.
    <|eot_id|><|start_header_id|>user<|end_header_id|>{question}
    <|eot_id|><|start_header_id|>assistant<|end_header_id|>
    """  # 질문과 답변 형식을 정의하는 템플릿
else:
    template = "### 질문: {question}### 답변:"

prompt_template = PromptTemplate.from_template(
    template
)  # 템플릿을 사용하여 프롬프트 객체 생성

# Step 5: LLMChain 생성
chain = prompt_template | llm
~~~

#### **2. `invoke`**
```
	1.`invoke`는 한 번에 입력 데이터를 처리하여 전한 응답을 반환하는 방식입니다.
	2. 사용자가 입력을 주면, 모델은 전체 결과를 생성한 후 한꺼번에 반환합니다.
	3. 모델이 생성하는 모든 텍스트가 완성된 후에 응답을 제공합니다.
```
~~~python
answer = chain.invoke({"question": "미국의 수도는 어디니?"})
print("Invoke Result:")
print(answer)
~~~
```
Invoke Result: <|begin_of_text|><|start_header_id|>system<|end_header_id|> 당신은 친절한 AI 어시스턴트입니다. 당신의 이름은 TITAN 입니다. 질문에 간단히 답해주세요. <|eot_id|><|start_header_id|>user<|end_header_id|>미국의 수도는 어디니? <|eot_id|><|start_header_id|>assistant<|end_header_id|> 미국 수도는 워싱턴 D.C.입니다.
```

#### **3. `stream`**
```
	1.`stream`는 모델이 텍스트를 생성하는 동안 부분적으로 결과를 실시간으로 반환하는 방식입니다.
	2. 모델이 응답 텍스트를 생성하는 과정을 실시간으로 확인할 수 있습니다.
```
~~~python
from langchain_core.messages import AIMessageChunk

# Step 6-2: `stream` 사용
print("Streamed Result:")
response = chain.stream({"question": "미국의 수도는 어디니?"})
answer = ""
for token in response:
    if isinstance(token, AIMessageChunk): #OpenAI return 타입
        print("1")
        answer += token.content
        # end="" 사용 목적 :
        # print 함수는 기본적으로 줄 끝에 줄바꿈 문자(\n, 개행)를 추가합니다.
        # 줄바꿈(\n) 대신 빈 문자열("")을 사용하도록 지정합니다.
        # 결과적으로 여러 출력이 한 줄로 이어져 출력됩니다.
        # flush=True 사용 목적 :
        # print 함수는 출력 데이터를 일시적으로 버퍼에 저장한 다음, 특정 조건(줄바꿈 등)에서 출력
        # flush=True 함으로써, 버퍼에 저장된 데이터를 즉시 출력하도록 강제
        print(token.content, end="", flush=True)
    elif isinstance(token, str): #로컬 모델 return 타입
        answer += token
        print(type(token))
        # print(token, end="", flush=True)
        print(token.split("<|end_header_id|>")[-1].strip())
    else:
        print(token["output"].split("<|end_header_id|>")[-1].strip())
~~~

```
Streamed Result:
<class 'str'>
미국 수도는 워싱턴 D.C.입니다.
```