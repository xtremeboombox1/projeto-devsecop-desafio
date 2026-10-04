# Desafio DevSecOps — Gerenciador de Tarefas

## Sobre o Projeto
Este repositório faz parte do desafio prático do módulo de DevSecOps da ADA Tech.
Você receberá este projeto com vulnerabilidades propositais e uma pipeline incompleta.
Seu objetivo é **implementar a pipeline de segurança** e **corrigir as vulnerabilidades**.

## Estado atual
A pipeline está **incompleta**. Os steps de segurança precisam ser implementados por você.

## Sua missão
1. Implementar os steps de segurança no `pipeline.yml`
2. Fazer a pipeline **quebrar** ao detectar os problemas
3. Corrigir as vulnerabilidades encontradas
4. Fazer a pipeline **passar** com tudo verde ✅
5. Documentar o funcionamento da pipeline neste README

## O que implementar
- [ ] Secrets Scanning com **Gitleaks**
- [ ] SAST com **Semgrep**
- [ ] SCA com **Grype**
- [ ] Assinatura do artefato com cosign
- [ ] Deploy com **GitHub Pages**

## Como a pipeline funciona
> Análise Inicial
Análise STRIDE framework 
•	S – Spoofing (Falsificação / Fingimento): 
o	    Risco de falsificação/fingimento de ser outro usuário pela posse das credenciais API_KEY e DB_PASSWORD exportas no código
•	T – Tampering (Adulteração / Violação): 
o	    Risco de manipulação das tarefas nesta aplicação por usuário não autorizado
•	R – Repudiation (Repúdio): 
•	I – Information Disclosure (Divulgação de Informação): 
o	    Risco de vazamento das credenciais API_KEY e DB_PASSWORD pois estão disponíveis no repositório github não privado
•	D – Denial of Service (Negação de Serviço): 
o	    Risco de queda de serviço por falta de rate limit da aplicação vulnerável a ataques DDoS

Análise Inicial de Ferramentas de Extensões de Segurança

Terminal
Problems:
{"error":"Failed to get dependencies for all 1 potential projects.\n\nc:\\Users\\user\\Desktop\\devsecops-desafio\\projeto-devsecop-desafio\\package.json:\nMissing node_modules folder: we can't test without dependencies.\nPlease run 'npm install' first.","path":"c:\\Users\\user\\Desktop\\devsecops-desafio"}
Análise Extensão Sonarqube

"API_KEY" detected here, make sure this is not a hard-coded secret. Sonarqube ln1, col17
Exemplo: 
const API_KEY = "1234567890abcdef"  // Noncompliant

const response = await fetch("https://api.my-service/v1/users", {
  headers: {
    Authorization: `Bearer ${API_KEY}`,
  },
});

const API_KEY = process.env.API_KEY;

const response = await fetch("https://api.my-service/v1/users", {
  headers: {
    Authorization: `Bearer ${API_KEY}`,
  },
});

Make sure that this dynamic injection or execution of code is safe. Sonarqube ln29, col5

Extensão Snyk – Análise
Open source – 54 issues: 2 critical, 20 high, 30 medium, 2low
package.json: 
- axios@0.21.1 crítica
- body-parser@1.19.0 alta
-lodash@4.17.4 alta
-qs@6.7.0 alta
-express@4.17.1 média
-path-to-regexp@0.1.7
-send@0.17.1 baixa

Extensão GitGuardian – Análise
Findings > No secrets detected yet
#1 commit: cc1ee93e7cc17072bade95cb2e9a2a526605b442
> Descreva cada step, o que ele faz e por que ele é importante para a segurança.
* criar chaves no repositório API_KEY e DB_PASSWORD para a segurança da aplicação, evitando vazamentos e usos indevidos
* na fase gitleaks irá verificar se existem chaves expostas e usando o princípio shift left impediria o prosseguimento do pipeline abortando o job
* sonarqube não acusou mais credencial exposta
#2 commit: 128bdc824d32ec061fc17f0ceefbc94c48c4d35d
GitHub Actions
Status  Duração Artefatos
FALHA   25s     2
-Set up job                  [ok]
-Baixar o artefato assinado  [ok]
-Instalar Cosign             [ok]
-Extrair site verificado     [ok]
-Configurar GitHub Pages     [X]

Resumo de Construção, Segurança e Assinatura
Nenhum vazamento detectado

*inclusão do semgrep
Terminal:  docker run --rm -v "${PWD}:/src" semgrep/semgrep semgrep scan --config auto --config p/xss --error .
Scan Status
Scanning 4 files tracked by git with 1067 code rules:
Scanning 4 files tracked by git with 1076 Code rules:

  Language      Rules   Files          Origin      Rules
 ─────────────────────────────        ───────────────────
  <multilang>      60       4          Community    1076
  js              154       1
  json              4       1
  html              1       1

em src/script.js
Javascript.browser.security.eval-detected.eval-detected
<<Blocking>>
Detected the use of eval(). eval() can be dangerous if used to evaluate dynamic contente. If this 
│ Scan Summary │
✅ Scan completed successfully.
 • Findings: 1 (1 blocking)
 • Rules run: 252
 • Targets scanned: 9
 • Parsed lines: ~100.0%
 • Scan was limited to files tracked by git
 • For a detailed list of skipped files and lines, run semgrep with the --verbose flag
Ran 252 rules on 9 files: 1 finding.Ensure evaluated contente is not definable by external sources
Details: https://sg.run/7ope
29 eval ‘console.log(“Tarefa adicionada: ‘ + input.value + ‘”);

## URL de Produção
> Adicione aqui o link do GitHub Pages após o deploy.
