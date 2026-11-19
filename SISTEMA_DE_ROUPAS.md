# Pokémon FireRed — Sistema de Roupas (personalização do treinador)

Baseado no decomp `pret/pokefirered`. Não inclui ROM nenhuma: o GitHub compila o código-fonte.

## Como compilar no GitHub (sem instalar nada)
1. Crie um repositório novo no GitHub e envie **todo o conteúdo desta pasta** (incluindo `.github/`).
2. Aba **Actions** → "Compilar ROM (FireRed + Roupas)" → o build roda sozinho (~5–10 min).
3. Quando terminar, abra a execução e baixe o artefato **pokefirered-roupas** → dentro está o `pokefirered.gba`.
4. Abra no emulador (mGBA, etc.) e jogue.

## Como usar no jogo
- **START → ARMÁRIO**: troca roupas que você já possui.
- **Loja Estilo** (Pallet Town, atendente no meio da cidade) e **Boutique Celadon** (Celadon City, na avenida
  perto da loja de departamentos): provador + compra. Cada loja vende itens diferentes (a Boutique tem os exclusivos).
- Controles: Cima/Baixo categoria · Esq/Dir peça (prévia em tempo real, de frente/costas/lado) ·
  A comprar (loja) · START confirmar · B cancelar.
- Itens **BLOQUEADOS** exigem missões (insígnias, Pokédex, Liga): Jaqueta Verde (Insígnia 3),
  Óculos Escuro (Insígnia 5), Jaqueta Preta (Insígnia 8), Chapéu Branco (Pokédex), Gorro Azul (Liga).

## Arquitetura (arquivos principais)
- `src/clothes.c` — tabela de itens, compositor de camadas, paleta dinâmica, posse/equipamento.
- `src/clothes_menu.c` — armário, provador e loja (UI).
- `src/data/clothes_items.h` — os 86 itens (nome, categoria, forma, cor, preço, loja, flag de missão).
- `graphics/clothes/*.png` — camadas de sprite (20 quadros 16x32: andar, correr, balançar a cabeça).
  Gerados por `tools/clothes_gen/gen_layers.py`; para usar arte própria basta substituir os PNGs (mesmo formato,
  índices 0–15 descritos no topo do script) — o motor não muda.
- Save: `struct ClothesSave` (32 bytes) ocupa o antigo `filler_B20` do `SaveBlock2`; o tamanho total e o
  layout do save não mudam (há `STATIC_ASSERT`).
- Ordem das camadas: mochila(trás) → corpo → calça → calçado → camiseta → jaqueta → cabelo → óculos → acessório →
  boné/chapéu (mochila vai à frente quando de costas). O contorno é gerado automaticamente.
- A EWRAM do FireRed está 99,6% cheia, então os 5 KB de quadros ficam no heap (`Alloc`) e só ~200 bytes de EWRAM são usados.

## Limitações conhecidas
- Moto, surf, pescaria e uso de item de campo ainda usam os sprites originais Red/Green.
- A arte das camadas é gerada por script (estilo próprio, simples); troque pelos seus PNGs para um visual mais caprichado.
- Os reflexos na água usam a paleta original do jogador.
- Carta de treinador e sprite de batalha continuam originais (o corpo Menino/Menina altera o gênero do treinador).
