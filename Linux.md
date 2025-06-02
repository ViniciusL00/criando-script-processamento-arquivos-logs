# 🐧 Módulo 1: Configurando Scripts e Permissões de Arquivos

## 📁 Criação de Diretórios no Linux

### ✅ Comando básico:

```bash
mkdir nome_do_diretorio
```

### ⚠️ Erro comum:

```bash
mkdir myapps/logs
# Saída: mkdir: cannot create directory ‘myapps/logs’: No such file or directory
```

Isso acontece porque o diretório `myapps` ainda **não existia**. O sistema não cria diretórios "pais" automaticamente.

### 🛠️ Soluções:

#### 🔸 Opção 1: Criar os diretórios um a um

```bash
mkdir myapps
mkdir myapps/logs
```

#### 🔸 Opção 2: Criar tudo de uma vez com `-p`

```bash
mkdir -p myapps/logs
```

O parâmetro `-p` cria todos os diretórios intermediários necessários.

---

## 📄 Criando e Editando Arquivos de Log

### ✏️ Criar um arquivo vazio:

```bash
touch myapp-backend.log
```

Ou crie com conteúdo direto:

```bash
echo "2024-09-01 10:05:21 ERROR: Database connection failed." > myapp-backend.log
```

### 📋 Inserir conteúdo grande de forma eficiente:

Usando redirecionamento com `cat`:

```bash
cat << EOF > myapp-backend.log
2024-09-01 10:05:21 ERROR: Database connection failed.
2024-09-01 10:06:10 INFO: Retrying database connection...
2024-09-01 10:06:15 INFO: Database connection established.
...
EOF
```

Tudo entre `<< EOF` e `EOF` será salvo dentro do arquivo.

### 🧠 Cuidado: Não execute arquivos `.log`

```bash
./myapp-backend.log
# Resultado: várias mensagens de "command not found"
```

Arquivos `.log` **não são scripts executáveis**! São apenas textos para leitura. O terminal tenta rodar cada linha como se fosse um comando. Resultado: erro em cima de erro!

### 📝 Editar manualmente (modo old school):

```bash
nano myapp-backend.log
```

Comandos no `nano`:

* `Ctrl+O` para salvar
* `Ctrl+X` para sair

---

## ⚙️ Criando Script para Processamento de Logs

### 📂 Estrutura inicial:

1. Criar um diretório para scripts:

```bash
mkdir scripts-linux
cd scripts-linux/
```

2. Criar o script com `vim`:

```bash
vim monitoramento-logs.sh
```

### 🧠 Modo de edição no `vim`:

* Pressione `i` para entrar no modo **insert** (edição)
* Digite o conteúdo do script
* Pressione `ESC` para sair do modo insert
* Para **salvar**: `:w`
* Para **sair**: `:q`

### 🖊️ Conteúdo básico do script:

```bash
#!/bin/bash

# Caminho do diretório de logs
LOG_DIR="../myapps/logs"

# Mensagem de verificação
echo "verificando logs no diretório $LOG_DIR"
```

### 🔐 Permissão de execução:

Após criar o script, torne-o executável:

```bash
chmod +x monitoramento-logs.sh
```

E execute com:

```bash
./monitoramento-logs.sh
```

---

## 🚀 Dicas do Terminal

* Sempre use `mkdir -p` para evitar erros com diretórios aninhados
* Para criar logs de teste, use `cat << EOF` com conteúdo dentro
* Nunca execute `.log` direto — visualize com `cat`, `less`, `nano` ou `tail`
* Use `vim` se quiser editar arquivos 😎

---

# 📁 Gerenciando Permissões no Linux

Este documento é um resumo detalhado da aula sobre permissões no Linux, com foco em scripts, notações e gerenciamento de acesso. 🎓🐧

---

## 🛠️ Alterando Permissões com `chmod`

### ✅ Comando usado:
```bash
chmod 755 monitoramento-logs.sh
```

Esse comando altera as permissões do arquivo `monitoramento-logs.sh`, permitindo que ele seja executado como um script.

### 📊 Significado do `755`:
- O número `755` está na **notação octal**.
- Ele representa as permissões separadas por **classes de usuários**:

| Classe       | Permissões                        | Descrição                          |
|--------------|-----------------------------------|------------------------------------|
| Usuário (7)  | rwx (leitura, escrita e execução) | Dono do arquivo tem total controle |
| Grupo (5)    | r-x (leitura e execução)          | Pode ler e executar                |
| Outros (5)   | r-x (leitura e execução)          | Pode ler e executar                |

---

## 👥 Classes de Usuário

| Classe       | Símbolo | Descrição                                     |
|--------------|---------|-----------------------------------------------|
| Usuário      | `u`     | O dono do arquivo                             |
| Grupo        | `g`     | Usuários que pertencem ao mesmo grupo do dono |
| Outros       | `o`     | Todos os outros usuários                      |
| Todos        | `a`     | Todos os usuários (`u`, `g` e `o`)            |

---

## 🔢 Notação OCTAL

| Número | Permissões | Significado            |
|--------|------------|------------------------|
| 0      | ---        | Nenhuma permissão      |
| 1      | --x        | Apenas execução        |
| 2      | -w-        | Apenas escrita         |
| 3      | -wx        | Escrita + execução     |
| 4      | r--        | Apenas leitura         |
| 5      | r-x        | Leitura + execução     |
| 6      | rw-        | Leitura + escrita      |
| 7      | rwx        | Todas as permissões    |

Exemplo:
```bash
chmod 764 arquivo.txt
```
- `7` (usuário): leitura, escrita, execução
- `6` (grupo): leitura, escrita
- `4` (outros): somente leitura

---

## ✍️ Notação SIMBÓLICA

Usa símbolos ao invés de números para atribuir permissões.

### Formato:
```bash
chmod [classe][operador][permissão] arquivo
```

| Operador | Significado                      |
|----------|----------------------------------|
| `+`      | Adiciona permissão               |
| `-`      | Remove permissão                 |
| `=`      | Define exatamente as permissões  |

### Exemplos:
```bash
chmod u+x script.sh     # Adiciona execução para o dono
chmod g-w script.sh     # Remove escrita do grupo
chmod o=r script.sh     # Define somente leitura para outros
```

---

## 🧠 Comparação: OCTAL vs SIMBÓLICA

| Critério         | Notação OCTAL             | Notação SIMBÓLICA                  |
|------------------|---------------------------|------------------------------------|
| Forma            | Numérica (`755`, `644`)   | Simbólica (`u+x`, `g-w`)           |
| Clareza          | Direta e compacta         | Mais expressiva e legível          |
| Controle fino    | Não muito flexível        | Flexível e específica por classe   |
| Uso mais comum   | Scripts, automações       | Ajustes pontuais e interativos     |

📌 Use **octal** quando quiser definir todas as permissões de uma vez.  
📌 Use **simbólica** quando quiser **modificar** permissões específicas sem mexer no resto.

---

## 📊 Explicando as permissões

### 📊 1: Permissões listadas
```bash
-rwxr-xr-x 1 user user 86 Jun  2 15:57 monitoramento-logs.sh
```
- `-rwxr-xr-x` = permissões:
  - `rwx` → usuário
  - `r-x` → grupo
  - `r-x` → outros
- `user user` → dono e grupo
- `86` → tamanho em bytes

### 📊 2: Executando o script
```bash
./monitoramento-logs.sh
```
- O script executa e exibe:
```
Verificando logs no diretório ../myapps/logs
```

Ou seja, a permissão de execução (`x`) está funcionando como esperado. ✅

---

## ✅ Conclusão

- Criar scripts com permissões corretas
- A diferença entre notações octal e simbólica
- Como usar `chmod` corretamente no terminal! 🥷💻
