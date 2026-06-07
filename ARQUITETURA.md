### 1. Arquitetura do Sistema

Para rodar localmente ou no GitHub (via Cloud Run/VPS), você deve separar a lógica em três pilares:

* **Ingestão de Vídeo (Backend):** Use **FastAPI** para gerenciar a comunicação com as câmeras. Você pode usar o **FFmpeg** (via Docker) para capturar o stream RTSP das câmeras e processá-lo.
* **Identidade:** Utilize **Firebase Authentication** (Google Sign-In). É o padrão de mercado para Flutter e permite que o usuário faça login com a conta Google de forma nativa e segura.
* **Armazenamento:** Utilize a **API do Google Drive**. Em vez de salvar no servidor local, o seu backend (ou a própria aplicação Flutter, dependendo da estratégia) fará o upload dos arquivos para pastas específicas no Drive do usuário.

### 2. Implementação Técnica Recomendada

#### A. O App (Frontend - Flutter)

* **Player de Vídeo:** Utilize o pacote `vlc_flutter_plugin` ou `fijkplayer`. Eles são robustos para lidar com streams de rede (RTSP/RTMP).
* **Autenticação:** Integre `google_sign_in` e `firebase_auth`.
* **Gerenciamento de Arquivos:** Use a `googleapis` package para Flutter. Com ela, você pode autenticar o usuário e realizar chamadas `drive.files.create` para enviar os vídeos capturados diretamente para o Drive.

#### B. Backend (Serviço Local ou Cloud - FastAPI)

* **Webhook de Câmera:** Sua API deve atuar como um orquestrador. Se a câmera for inteligente, ela pode enviar um *trigger* (evento de movimento) via HTTP para sua API.
* **Google Drive Integration (Service Account):**
* No seu projeto Google Cloud, crie uma **Service Account** com permissões para o Drive.
* Sua API usará essa credencial para criar pastas automáticas por data ou por câmera no Drive do usuário.
* *Dica:* Para economizar banda, não faça o stream passar pelo servidor; apenas processe os metadados e os eventos de gravação.

### 3. Roteiro de Desenvolvimento (MVP)

1. **Fase 1 (Autenticação):** Configure o Firebase no seu projeto Flutter com Login via Google.
2. **Fase 2 (Conexão):** Crie uma interface no Flutter que peça apenas o endereço IP e a porta RTSP da câmera. Teste a visualização em tempo real (Live Preview).
3. **Fase 3 (Integração Drive):** Implemente a biblioteca `googleapis` para listar pastas do Drive. Crie uma função que pegue um arquivo local (`.mp4`) e faça o upload para uma pasta chamada `Monitoramento_MAZZ`.
4. **Fase 4 (Automação):** No backend, crie um *worker* com **Docker** que detecte quando a câmera disparou um alerta e acione o script de upload.

### Considerações Críticas

* **Latência:** Fazer o upload de vídeo em tempo real para o Drive pode consumir muita banda de *upload*. Recomendo implementar uma lógica de **buffer local**: grave em um volume Docker no seu servidor local, e faça o upload para o Google Drive em background (batch process) a cada 5 ou 10 minutos.
* **Privacidade:** Como você lida com monitoramento, lembre-se de que, ao usar o Google Drive como storage, você está sujeito aos termos de serviço da Google. Certifique-se de que a autenticação esteja sempre atrelada ao dono da câmera.