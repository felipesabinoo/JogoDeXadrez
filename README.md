# ProjectXadrez

Jogo de xadrez de console implementado em C# direcionado ao .NET 8 (C# 12).  
Implementa regras de xadrez padrão completas com um mecanismo pequeno e bem estruturado
---

## Resumo
- Linguagem: `C# 12`  
- Framework: `.NET 8`  
- UI: Console (arquivo `Tela.cs`)  
- Regras implementadas: legal moves, check, checkmate, castling (short/long), en passant, pawn promotion (automatic to queen).

---

## Arquitetura & Principais Conceitos

- Camadas
  - `tabuleiro/` — modelo genérico do tabuleiro e peças: `Tabuleiro`, `Peca`, `Posicao`, `PosicaoXadrez`, `TabuleiroException`, `Cor`.
  - `xadrez/` — regras do jogo: `PartidaDeXadrez`, peças específicas (`Rei`, `Dama`, `Torre`, `Bispo`, `Cavalo`, `Peao`) e lógica (movimentos especiais).
  - Root — `Program.cs` e `Tela.cs` (entrada e interação com usuário).

- Padrões e princípios aplicados
  - Herança + polimorfismo: tipo base `Peca` com método abstrato `movimentosPossiveis()`; cada peça implementa sua lógica.
  - Encapsulamento: propriedades com `private set` para controle de estado (`turno`, `jogadorAtual`, `terminada`).
  - Single Responsibility / Separation of Concerns:
    - `Tabuleiro` trata da estrutura e operações de baixo nível (colocar/retirar peça, validar posição).
    - `PartidaDeXadrez` orquestra regras de jogo, turnos e jogadas especiais.
    - `Tela` trata apenas de entrada/saída no console.
  - Uso de coleções apropriadas: `HashSet<Peca>` para conjunto de peças em jogo / capturadas (operações de conjunto eficientes).
  - Tratamento de erros com exceções específicas: `TabuleiroException`.

- Estruturas de dados importantes
  - Matriz 2D (`Peca[,]`) em `Tabuleiro` para representação do tabuleiro.
  - `Posicao` (linha/coluna) e `PosicaoXadrez` (notação 'a1') — conversão entre domínio e entrada do usuário.
  - Matrizes booleanas retornadas por `movimentosPossiveis()` para validar destinos.

---

## Operações e fluxo principal

- Movimentação
  - `PartidaDeXadrez.realizaJogada(origem, destino)` — executa a jogada completa:
    - chama `executarMovimento()` (move, captura, trata roque e en passant),
    - valida se não deixou o jogador em xeque (desfaz movimento se inválido),
    - trata promoção de peão,
    - atualiza flags (`xeque`, `terminada`, `vulneravelEnPassant`) e alterna jogador.
  - `executarMovimento(origem, destino)` — move a peça, contabiliza movimentos e aplica efeitos especiais (roque, en passant).
  - `desfazMovimento(origem, destino, pecaCapturada)` — reverte movimentos (usado para validações como teste de xeque).

- Validação
  - `validarPosicaoDeOrigem(Posicao pos)` — garante peça presente, pertence ao jogador atual e tem movimentos possíveis.
  - `validarPosicaoDeDestino(Posicao origem, Posicao destino)` — garante que destino é permitido pela peça selecionada.

- Estado e verificação de xeque/xeque-mate
  - `estaEmXeque(Cor cor)` — encontra o rei e verifica se qualquer peça adversária tem movimento para a posição do rei.
  - `testeXequemate(Cor cor)` — para cada movimento possível de cada peça tenta executar e testa se ainda está em xeque (backtracking com `desfazMovimento`).

---
