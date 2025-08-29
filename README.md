# PaperRetrieval

## 💻 프로젝트 소개

### \<프로젝트 소개\>

  - 이 프로젝트는 특정 연구 논문을 시작점으로 하여, 그와 관련된 후속 연구들을 효율적으로 탐색하고 분석할 수 있도록 돕는 RAG(검색 증강 생성) 시스템입니다. 사용자가 기준이 되는 논문을 검색하면, 시스템은 데이터베이스와 웹을 통해 해당 논문 정보를 확보합니다. 이후 LangGraph로 구축된 워크플로우를 통해 관련 후속 논문들을 검색하고, 사용자는 챗봇 인터페이스를 통해 후속 연구들에 대한 심층적인 질문을 할 수 있습니다.

### \<작품 소개\>

  - 'Paper Follow-up Researcher'는 연구자들이 특정 논문의 후속 연구 동향을 빠르고 정확하게 파악할 수 있도록 설계된 웹 애플리케이션입니다. Gradio 기반의 UI를 통해 논문을 검색하고, Upstage LLM을 활용한 RAG 파이프라인이 후속 연구 정보를 분석하여 구조화된 답변을 제공합니다. 이를 통해 연구 생산성을 높이고 새로운 아이디어를 얻는 데 도움을 주는 것을 목표로 합니다.



## 👨‍👩‍👦‍👦 팀 구성원

| ![a1](https://avatars.githubusercontent.com/u/114648763?v=4) | ![avatar2](https://avatars.githubusercontent.com/u/113088511?v=4) | ![avatar3](https://avatars.githubusercontent.com/u/155069538?v=4) | ![avatar4](https://avatars.githubusercontent.com/u/205017707?v=4) | ![avatar5](https://avatars.githubusercontent.com/u/83211745?v=4) |
| :--------------------------------------------------------------: | :--------------------------------------------------------------: | :--------------------------------------------------------------: | :--------------------------------------------------------------: | :--------------------------------------------------------------: |
|            [김정빈](https://github.com/JBreals/JBreals)             |            [송규헌](https://github.com/skier-song9)             |            [이나경](https://github.com/imnaagyeong)             |            [조선미](https://github.com/LearnSphere-2025)             |            [편아현](https://github.com/vusdkvus1)             |
|                            팀장, DB 서버 구성, 노드 기능 구현, API 서버 개선                              |                            LangGraph 설계 및 구현, generation 구현, gradio webapp 베이스라인 작성                            |                            데이터 수집                             |                            LangGraph 실행 및 평가                          |                           데이터 수집, 노드 기능 구현                              |



## 🔨 개발 환경 및 기술 스택

  - **주 언어**: Python
  - **핵심 프레임워크**: Langchain, LangGraph, FastAPI, Gradio
  - **LLM 및 임베딩**: Upstage API (Solar), Qwen/Qwen3-Embedding-0.6B
  - **데이터베이스**: PostgreSQL + pgvector
  - **검색**: Tavily Search API, OpenAlex API
  - **개발 및 배포**: Docker, uvicorn, gunicorn
  - **버전 및 이슈관리**: Git, Github
  - **협업 툴**: Github



## 📁 프로젝트 구조

```
upstageailab-langchain-pjt-langchain_6/
├── services/ # 각 마이크로서비스 애플리케케이션의 코드가 위치하는 메인 디렉토리입니다. 
│   ├── paper-ingestor/  # (Phase 0: 데이터 수집 서비스)
│   │   ├── Dockerfile
│   │   ├── requirements.txt
│   │   └── src/
│   │       ├── __main__.py
│   │       ├── config.py
│   │       ├── database.py
│   │       ├── embedding.py
│   │       ├── scheduler.py
│   │       └── source_api.py
│   │
│   ├── rag-api/  # (Phase 1 & 2: RAG API 서비스)
│   │   ├── Dockerfile
│   │   ├── requirements.txt
│   │   ├── visualize.py
│   │   └── src/
│   │       ├── __main__.py
│   │       ├── api/
│   │       │   ├── endpoints.py
│   │       │   └── schemas.py
│   │       ├── config.py
│   │       ├── graph/
│   │       │   ├── builder.py
│   │       │   ├── nodes.py
│   │       │   └── state.py
│   │       └── core/
│   │           ├── database.py
│   │           ├── llm.py
│   │           ├── retriever.py
│   │           └── source_api.py
│   │
│   └── webapp/ # Gradio web application
│       ├── requirements.txt
│       └── src/
│           └── app.py
├── packages/
│   └── common/
│       └── models.py
│
├── .env
├── .gitignore
├── docker-compose.yml
└── README.md
```



## 💻​ 구현 기능

### Phase 1: 기준 논문 검색 및 확보

  - **DB 검색**: 사용자가 입력한 논문 제목을 기반으로 로컬 PostgreSQL 데이터베이스에서 일치하는 논문을 검색합니다.
  - **웹 검색 및 수집**: DB에 논문이 없을 경우, OpenAlex API를 통해 웹에서 논문 정보를 검색합니다.
  - **DB 저장**: 검색된 논문의 메타데이터(제목, 초록, 저자 등)와 인용 정보를 추출하고, 초록을 임베딩하여 데이터베이스에 저장합니다.

### Phase 2: 후속 연구 탐색 및 질의응답

  - **RAG 파이프라인**: LangGraph로 설계된 워크플로우를 통해 사용자의 질문에 대한 답변을 생성합니다.
  - **질문 증강**: 사용자의 질문에서 핵심 키워드를 추출하고 Tavily 검색으로 보강하여 검색 성능을 향상시킵니다.
  - **후속 연구 검색**: 기준 논문을 인용한 논문들을 DB에서 검색하고, 증강된 질문 벡터와 유사도가 높은 문서를 우선적으로 추출합니다.
  - **답변 생성**: 검색된 후속 연구 내용을 바탕으로 Upstage LLM이 사용자의 질문에 대한 종합적인 답변을 생성합니다.
  - **웹 UI 제공**: Gradio를 사용하여 사용자가 쉽게 논문을 검색하고 질문할 수 있는 채팅 인터페이스를 제공합니다.



https://github.com/user-attachments/assets/8a5c2b2f-9096-4691-8edf-ca9beaf23719



## 🛠️ 작품 아키텍처(필수X)

![architecture](https://github.com/AIBootcamp13/upstageailab-langchain-pjt-langchain_6/blob/main/docs/architecture.png)



## 📌 프로젝트 회고

    - 김정빈 : LangGraph 노드들의 기능을 구현하고 실제로 흐름을 따라가보며 개발을 하니 agent가 어떤 식으로 동작하는지 이해할 수 있었습니다. 아직 생성 평가나 Langsmith 같은 로깅 프레임워크도 적용해보지 못했는데, 다양한 것들을 적용해보면서 깊이 있는 agent를 개발해보고 싶은 마음이 생겼습니다. 

    - 송규헌 : LangGraph를 처음 구현해보며 시행착오를 겪었고, 다음부터는 더 나은 설계를 할 수 있을 것 같습니다. 실제 프로덕트를 구현하기 위해 더 정교한 설계가 필요하다는 사실을 다시 깨닫게 되었습니다.

    - 이나경 : 랭체인 프로젝트를 진행하면서 DB와 LLM을 실제로 연결해 서비스화하는 과정을 경험할 수 있었습니다. 팀원들 덕분에 많은 인사이트를 얻을 수 있었습니다.

    - 편아현 : 팀원들 덕분에 프로젝트를 잘 끝낼 수 있었고, DB를 실제로 사용하는 방법과 LangChain, LangGraph에 대한 이해도를 높일 수 있는 시간이였습니다.

    - 조선미 : 랭체인/랭그래프 프로젝트를 통해서 깊이 있게  개념을 이해할 수 있었습니다. 후속 논문을 찾아주는 흥미로운 주제를 탐구하는 즐거운 시간이였습니다. :)



## 📰​ 참고자료

  - [LangGraph Docs](https://langchain-ai.github.io/langgraph/concepts/why-langgraph/)
  - [LangChain Tutorial](https://wikidocs.net/book/14314)
  - [OpenAlex](https://docs.openalex.org/how-to-use-the-api/api-overview)
