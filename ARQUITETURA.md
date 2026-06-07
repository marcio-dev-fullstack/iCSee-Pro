# 🛡️ iCSee-Pro | Documentação de Arquitetura

**Projeto:** Monitoramento de Alta Disponibilidade

**Plataforma:** iCSee-Pro

**Desenvolvedor:** RAZGO Tecnologia

---

## 1. Visão Geral da Arquitetura

O sistema **iCSee-Pro** é estruturado em três pilares fundamentais, desenhados para alta escalabilidade e integração nativa com o ecossistema Google:

* **Ingestão (Backend):** Utiliza **FastAPI** como orquestrador, com processamento de streams RTSP via **FFmpeg** em ambiente Docker.


* **Identidade:** Implementa **Firebase Authentication** com Google Sign-In, garantindo uma camada de autenticação segura e nativa.


* **Armazenamento:** Integração direta com a **API do Google Drive** para persistência em nuvem, eliminando a dependência de armazenamento local.



---

## 2. Especificações Técnicas

### 📱 Frontend (Flutter)

* **Streaming:** Utilização de `vlc_flutter_plugin` ou `fijkplayer` para suporte robusto a protocolos RTSP/RTMP.


* **Autenticação:** Integração dos pacotes `google_sign_in` e `firebase_auth`.


* **File Management:** Uso da biblioteca `googleapis` para gerenciar chamadas de `drive.files.create`.



### ⚙️ Backend (FastAPI)

* **Orquestração:** O serviço atua como um hub para *webhooks*, processando eventos de movimento disparados por câmeras inteligentes.


* **Google Drive Integration:** Implementação via **Service Account** no Google Cloud Console, permitindo a criação dinâmica de pastas.


* **Otimização de Banda:** O sistema processa apenas metadados e eventos no servidor, evitando a sobrecarga de tráfego de stream.



---

## 3. Roteiro de Implementação (MVP)

| Fase | Objetivo | Ação Principal |
| --- | --- | --- |
| **01** | **Autenticação** | Configuração do Firebase com Login via Google.

 |
| **02** | **Conectividade** | Interface Flutter para IP/Porta RTSP e *Live Preview*.

 |
| **03** | **Integração Drive** | Implementação de `googleapis` para upload automático na pasta `Monitoramento_MAZZ`.

 |
| **04** | **Automação** | *Worker* Docker para detecção de alertas e disparo de upload.

 |

---

## ⚠️ Considerações Críticas

> **Latência e Performance:** O upload em tempo real pode saturar a largura de banda. Recomenda-se a utilização de **buffer local** em volumes Docker, com processamento em *batch* programado a cada 5 ou 10 minutos.
> 
> 

> **Conformidade e Privacidade:** A integração com o Google Drive está sujeita aos Termos de Serviço da Google. É mandatório que a autenticação esteja sempre vinculada ao proprietário da câmera, garantindo a integridade dos dados.