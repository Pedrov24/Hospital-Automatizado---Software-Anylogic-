# Hospital Automatizado – AnyLogic

## Visão Geral

Este projeto consiste no desenvolvimento de um modelo de **Simulação Em Tempo Real (STR)** utilizando o **AnyLogic**, com o 
objetivo de representar o funcionamento de um hospital automatizado.

O sistema modela o fluxo completo dos pacientes desde a entrada no hospital até a alta médica, considerando:

- Classificação de risco (triagem);
- Filas prioritárias;
- Transporte por maqueiro;
- Atendimento em consultórios;
- Recuperação pós-atendimento;
- Alta hospitalar.

---

# Objetivo

Desenvolver um sistema capaz de simular um ambiente hospitalar onde pacientes são classificados por nível de urgência e atendidos 
conforme sua prioridade clínica, respeitando também a ordem de chegada dentro de cada categoria.

---

# Arquitetura desenvolvida do Sistema

<img width="1360" height="573" alt="image" src="https://github.com/user-attachments/assets/fc67dc1f-cb4e-4bec-ba73-01e9fb0f6e43" />

# Estrutura dos Agentes

## Paciente

Cada paciente possui atributos próprios:

| Variável | Descrição |
|-----------|------------|
| idPaciente | Identificador único |
| cor | Classificação de risco |
| prioridade | Nível de urgência |
| prioridadeFila | Valor utilizado para ordenação |
| consultorioEscolhido | Consultório de destino |

---

# Sistema de Triagem

A classificação de risco é gerada por distribuição probabilística:

| Cor | Prioridade |
|------|------------|
| Vermelho | 1 |
| Laranja | 2 |
| Azul | 3 |

```java
double sorteio = random();

if(sorteio < 0.3){
    agent.cor = "Vermelho";
    agent.prioridade = 1;
}
else if(sorteio < 0.6){
    agent.cor = "Laranja";
    agent.prioridade = 2;
}
else{
    agent.cor = "Azul";
    agent.prioridade = 3;
}
```

---

# Sistema de Prioridade

## Problema Inicial

Durante o desenvolvimento foi observado que a fila se comportava como FIFO (First In First Out), ignorando a gravidade dos pacientes.

## Solução

Foi criada a variável:

```java
prioridadeFila
```
Calculada por:
```java
agent.prioridadeFila =
agent.prioridade * 100000 + time();
```

### Resultado

Menor valor = maior prioridade.

Exemplos:

| Cor | Valor aproximado |
|------|------|
| Vermelho | 100000 |
| Laranja | 200000 |
| Azul | 300000 |

Assim:

```text
Vermelho
↓
Laranja
↓
Azul
```
# Critério de Desempate

Além da prioridade clínica, o sistema respeita a ordem de chegada.

Exemplo:

```text
Paciente Vermelho A = 100002
Paciente Vermelho B = 100003
```

O primeiro paciente vermelho continua sendo atendido primeiro.

# Sistema de Transporte

Foi modelado um recurso compartilhado:

## Maqueiro

Responsabilidades:

- Retirar pacientes da fila;
- Transportar para os consultórios;
- Transportar pacientes para recuperação.

Fluxo:

```text
Triagem
↓
Maqueiro
↓
Consultório (C1/C2)
```
---

# Consultórios

O sistema possui:
- Consultório 1 e 2 
A distribuição dos pacientes ocorre através do bloco:

```text
entrega_de_paciente
```
Funcionando como uma chave seletora se atentando para se caso um consultório estiver ocupado o maqueiro, encaminhar o paciente para o outro
consultório

# Atendimento Médico

Estrutura utilizada:

```text
C1/C2
 ↓
tempo_atendimento(1/2)
 ↓
saida_consultorio(1/2)
```

Representando:

- captura do recurso;
- atendimento;
- liberação do recurso.

---

# Recuperação

Após o atendimento:

```text
 Fila_sala_recuperacao(1/2)
      |
      v
 recuperacao(1/2)
      |
      v
 alta_paciente(1/2)
```

A recuperação possui capacidade limitada, simulando leitos hospitalares.

---

## Funcionalidades concluídas
-  Entrada de pacientes
-  Triagem automática
-  Classificação de risco
-  Fila prioritária
-  Desempate por ordem de chegada
-  Transporte por maqueiro
-  Encaminhamento para consultórios
-  Atendimento médico
-  Recuperação
-  Alta hospitalar
-  Sistema de logs
-  Correção do SelectOutput
-  Validação completa do fluxo

# Melhorias Futuras

## Expansão do Modelo

- Especialidades médicas;
- Exames laboratoriais;
- Internação;
- Centro cirúrgico;
- Emergência.

# Link para Dowmload da Ferramenta AnyLogic
```text
https://www.anylogic.com/downloads/
```
