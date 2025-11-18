# 📘 DermaIA – IA para Prevenção e Suporte no Diagnóstico do Câncer de Pele

## 🧬 Sobre o Projeto
O **DermaIA** é um sistema baseado em Inteligência Artificial desenvolvido para auxiliar na **prevenção**, **triagem inicial** e **suporte ao diagnóstico** de possíveis casos de câncer de pele. Ele combina análise de imagens, recursos de geolocalização, relatórios clínicos e gamificação para oferecer uma solução completa para usuários comuns, médicos e hospitais.

---

## 🩺 Problema
- Muitas pessoas notam manchas suspeitas na pele, mas **não sabem se devem procurar um especialista**.
- Atrasos na busca por atendimento podem prejudicar o **diagnóstico precoce**, essencial para aumentar as chances de cura.
- Hospitais e médicos enfrentam **alta demanda e filas**, dificultando a triagem eficiente.
- A falta de informação acessível gera medo, insegurança e negligência.

---

## 💡 Solução DermaIA
Um aplicativo inteligente que:
- Analisa fotos de manchas através de **Deep Learning**.
- Classifica como **baixa** ou **alta sugestão de investigação**.
- Fornece orientações claras sobre próximos passos.
- Oferece relatórios, dashboards e histórico evolutivo para **médicos e hospitais**.

---

## 🛠️ Funcionalidades Principais
### 👤 Para usuários comuns
- **Análise instantânea** de manchas usando IA.
- Classificação simples: **baixa** ou **alta sugestão**.
- **Mapa de clínicas e hospitais próximos**.
- **Alertas climáticos e UV**: lembrete para uso de protetor solar.
- **Gamificação**: ranking familiar de proteção solar.

### 🥼 Para médicos
- Histórico de imagens por paciente.
- Relatórios clínicos detalhados.
- Dashboard com acompanhamento da evolução das lesões.

### 🏥 Para hospitais e clínicas
- Dashboard multiusuário.
- Relatórios centralizados.
- Integração com sistemas de saúde.
- Suporte técnico especializado.

---

## 💰 Modelo de Negócio
### **Plano Gratuito – Usuário Comum**
- Análise básica de IA.
- Classificação baixa/alta sugestão.
- Alertas UV.

### **Plano Profissional – Médicos (Pago)**
- IA avançada.
- Relatórios clínicos completos.
- Dashboard de pacientes.
- Comparação evolutiva de imagens.

### **Plano Premium – Hospitais/Clínicas (Pago)**
- Dashboard multiusuário.
- Relatórios centralizados.
- Integração com sistemas médicos.
- Suporte especializado.

---

## 🌟 Diferenciais do Projeto
- **Acessível** a qualquer pessoa com um celular.
- **Prevenção ativa** com lembretes e gamificação.
- **Foco social**, ajudando na detecção precoce.
- **Escalável**: desde usuários individuais até hospitais.
- **Suporte profissional** com ferramentas avançadas.

---

## 🤖 Tecnologias e Bibliotecas Utilizadas

---

## 🔍 Explicação Técnica do Código do Modelo de IA
A seguir estão **todas as informações importantes sobre como o código do modelo funciona**, explicadas de forma clara e profissional. O código completo não será colocado aqui, apenas a explicação técnica.

### 🔹 1. Estrutura das Pastas
O modelo utiliza três diretórios principais com imagens divididas em duas classes:
- **TRAIN_DIR** → imagens usadas para treino
- **VAL_DIR** → imagens usadas para validação
- **TEST_DIR** → imagens usadas para teste

Cada diretório contém duas classes:
- **0 – Baixa sugestão de investigação (benigno)**
- **1 – Alta sugestão de investigação (maligno)**

O TensorFlow detecta essas classes automaticamente.

---

### 🔹 2. Criação dos Datasets
O código utiliza `image_dataset_from_directory` para:
- Carregar as imagens
- Redimensionar para **224×224**
- Criar batches de **32** imagens
- Aplicar *shuffle* no conjunto de treino
- Padronizar o formato de entrada

Isso garante que as imagens estejam corretamente organizadas para o modelo.

Além disso, datasets são otimizados com:
- **cache()** → acelera o acesso
- **prefetch()** → pipeline mais rápido

---

### 🔹 3. Data Augmentation
Para evitar *overfitting*, o código aplica aumentos artificiais no dataset:
- Flips horizontais e verticais
- Rotações leves
- Zoom aleatório

Esses aumentos tornam o modelo mais robusto para fotos tiradas por usuários comuns.

---

### 🔹 4. Arquitetura do Modelo (EfficientNetB0)
O modelo usa o **EfficientNetB0**, uma CNN moderna e leve com ótimo desempenho em imagens médicas.

Passos da construção:
- Carrega EfficientNetB0 pré-treinada no ImageNet
- Remove o topo (camadas de classificação originais)
- Adiciona:
  - Camada de *GlobalAveragePooling2D*
  - *Dropout* 0.3 para reduzir *overfitting*
  - Camada final com **sigmoid** (saída binária 0/1)

Inicialmente, o EfficientNet é **congelado** — apenas as camadas finais treinam.

---

### 🔹 5. Treinamento – Fase 1
O modelo é treinado com:
- Otimizador **Adam** (lr = 1e-4)
- Função de perda **binary_crossentropy**
- Métricas:
  - **AUC** (principal para modelos médicos)
  - **accuracy**

Callbacks utilizados:
- **EarlyStopping** → evita overfitting
- **ModelCheckpoint** → salva o melhor modelo
- **ReduceLROnPlateau** → reduz a taxa de aprendizado se necessário

---

### 🔹 6. Cálculo Automático dos Pesos das Classes
Caso haja mais imagens de uma classe do que da outra, o código calcula automaticamente os **class weights**.

Isso ajuda o modelo a não aprender mais sobre uma classe do que a outra, melhorando a sensibilidade.

---

### 🔹 7. Fine-Tuning – Fase 2
Após o treino inicial, parte do EfficientNet é **descongelada**:
- Apenas as **últimas 50 camadas** são treinadas
- Taxa de aprendizado menor: **1e-5**
- Treinamento agora utiliza `class_weight`

Essa etapa melhora muito a detecção de padrões dermatológicos mais difíceis.

---

### 🔹 8. Avaliação Completa do Modelo
O modelo gera:
- Loss
- AUC no teste
- Acurácia
- Matriz de confusão
- Relatório de classificação (precision, recall, f1-score)
- Probabilidades reais da curva ROC

Essa parte valida se o modelo é confiável.

---

### 🔹 9. Predição de Imagem Única
O código contém uma função para prever **qualquer foto enviada pelo usuário**:
- Redimensiona
- Normaliza
- Passa pelo modelo

A saída mostra:
- Classe prevista
- Confiança da predição

---

### 🔹 10. Exportação do Modelo
O modelo é exportado em vários formatos para uso no app e servidores:
- `.weights.h5`
- `.keras`
- **TensorFlow SavedModel**
- **ONNX** (compatível com apps mobile, C++, C#, JS, Unity, etc.)

---

### 🧠 IA / Deep Learning
- EfficientNet (alta acurácia para imagens médicas)
- Vision Transformer (ViT)

### 🛠️ Frameworks
- TensorFlow / Keras
- PyTorch
- scikit-learn
- OpenCV

### 🗂️ Bancos de dados utilizados
- ISIC Archive
- HAM10000
- AnatPat (Unicamp)

---

## 🌍 ODS (Objetivos de Desenvolvimento Sustentável)
- **ODS 3** – Saúde e Bem-Estar
- **ODS 10** – Redução das Desigualdades
- **ODS 17** – Parcerias
- **ODS 18** – Igualdade Étnico-Racial

---

## 🔁 Fluxo Simplificado do DermaIA
1. Usuário tira foto da mancha.
2. IA analisa com modelo de Deep Learning.
3. Resultado:
   - **Baixa sugestão** → acompanhamento + lembretes.
   - **Alta sugestão** → mapa de hospitais + telemedicina.

---

## 🎯 Impacto Esperado
- Redução de atrasos no diagnóstico precoce.
- Acesso facilitado a informações médicas confiáveis.
- Aumento da eficiência na triagem feita por médicos.
- Fortalecimento de políticas e campanhas de prevenção.
- Contribuição para redução de desigualdades em saúde.

---

## 📎 Autor
**DermaIA – Tecnologia a favor da vida desde o primeiro sinal.**



