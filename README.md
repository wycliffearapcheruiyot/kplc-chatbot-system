# KPLC Chatbot System

This repo is the map. The KPLC chatbot is split across several repos; this page explains how they connect.

## Architecture

```
kplc-chatbot-web (Vercel)   --+
                              +--> kplc-chatbot-gateway --+--> kplc-chatbot-inference (Kaggle notebook)
kplc-chatbot-admin (Netlify)--+                           +--> kplc-chatbot-dataset-sync (Kaggle <- HF)
                                                          +--> kplc-chatbot-db-infra (MongoDB Atlas)
```

## Repos

| Repo | Role |
|---|---|
| [kplc-chatbot-web](https://github.com/wycliffearapcheruiyot/kplc-chatbot-web) | Next.js chatbot UI (end users talk to this) |
| [kplc-chatbot-admin](https://github.com/wycliffearapcheruiyot/kplc-chatbot-admin) | React admin panel for managing the bot |
| [kplc-chatbot-gateway](https://github.com/wycliffearapcheruiyot/kplc-chatbot-gateway) | FastAPI router — the one service both frontends call |
| [kplc-chatbot-inference](https://github.com/wycliffearapcheruiyot/kplc-chatbot-inference) | Starts/manages the Kaggle notebook that runs the model |
| [kplc-chatbot-dataset-sync](https://github.com/wycliffearapcheruiyot/kplc-chatbot-dataset-sync) | Ensures the model dataset exists on Kaggle, pulling from Hugging Face if missing |
| [kplc-chatbot-kb-builder](https://github.com/wycliffearapcheruiyot/kplc-chatbot-kb-builder) | Builds/verifies the chatbot's knowledge-base data |
| [kplc-chatbot-db-infra](https://github.com/wycliffearapcheruiyot/kplc-chatbot-db-infra) | MongoDB Atlas provisioning/config for shared state |

All repos in this system also carry the `kplc-chatbot-system` topic tag — [browse them here](https://github.com/wycliffearapcheruiyot?tab=repositories&q=topic%3Akplc-chatbot-system).

## Data flow (summary)

1. User asks a question in the web UI or admin panel.
2. The gateway checks whether the model dataset exists on Kaggle (via dataset-sync, which pulls from Hugging Face if it's missing).
3. The gateway starts the Kaggle notebook via the inference service.
4. Once ready, chat questions flow gateway -> inference -> Kaggle GPU, with retrieval/session state stored via db-infra (MongoDB Atlas).
