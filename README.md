# Project Protostar Guidelines

## 📂 Structure
- `/home/hansol/workspace/project-protostar` : 프로젝트의 루트 폴더로 프로젝트를 위해 사용되는 모든 내용이 담겨져 있다. 이하 내용에서의 모든 경로는 해당 경로를 기준 이하 경로다.
- `~/docs` : 프로젝트의 개발을 위한 작업 문서들이 포함되어 있다.  
    - `~/docs/guide` : AI Antigravity 를 위한 특정 설정들이 기재되거나, 개발 룰 등을 지정하기 위한 공간. **AI 는 편집할 수 없다.**
    - `~/docs/history` : 프로젝트의 작업 내역을 기록하는 문서 모음, Antigravity 를 기반으로 작업했던 사항의 기록을 작성한다. **AI 는 편집할 수 있다.**
    - `~/docs/project-official` : 개발을 위해 직접 기획 및 설계했던 내용들을 담아둔 공식 공간 <-> Obsidian 프로젝트와 함께하여 관리됨. Obsidian에서 제작 및 이후 완성된 본만 여기에 기록됨. **AI 는 편집할 수 없다.**
    - `~/docs/design` : 개발을 위한 디자인 문서들이 포함되어 있다. **AI 는 편집할 수 없다.**

## 📝 Guidelines
- AI 는 `guide` 폴더를 확인하고, 거기에 맞춰 진행한다. (세부 내용은 해당 폴더를 참고할 것. )
- AI 는 작업 후, 해당 작업에 대해 진행 사항을 정리하고 `history` 폴더에 기록한다.

## Project Protostar 

### 개요
- Agentic AI 구조를 차용한, 개인 커리어 비서 프로젝트.
- AI 를 기반으로 자료를 업로드하면 RAGGING 및 AI가 이용가능한 독자적인 데이터 구조로 파싱 되며, 이를 기반으로 이용자에게는 자신의 커리어를 적절하고 효율적으로 제시 할 수 있음
- HR 담당자는 자유롭게 기술블로그의 자료를 요약하거나, 자료에 대한 질문을 통해 보다 빠르고 효과적이게 자료를 판단해볼 수 있습니다. 

### 프로젝트 특징 
- Containerizing 을 기반으로 구축된 고가용성, 확장성, 안정성을 가진 서버
- Zero downtime 의 Green/ Blue Deployment(Jenkins) 방식의 안정적인 서비스 제공 및 운영 기능
- On Premise 기반 SSOT 문제를 고려한 Admin, Service 레이어 구분 및 AWS 호환성을 고려한 인프라(MinIO, etc) 구축을 통한 높은 이식 가능성 서버
- 목적에 따른 NestJS, FastAPI 기반의 폴리글랏 구조의 서버를 통해 요구되는 기술 최적화 설계 지향
- k8s 기반의 scale out 을 접목(계획)을 통한 완전한 고가용성 및 대용량 처리 가능 서버