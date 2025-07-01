**Resumo direto**

* **Boa para inspeções pontuais ou de baixa velocidade** (ex.: medir gargalo, presença de frisos, ver se o “gate” está fechado).
* **Insuficiente para 100 % de inspeção on-line** em pré-formas de bebidas – como exige a ABNT NBR 15588 :2008 e as boas práticas da cadeia de refrigerantes/água – porque resolução, profundidade de inspeção e algoritmos não atingem o nível de 0,10 – 0,20 mm típico para manchas, bolhas ou cristalinidade.
* Se o seu objetivo é uma máquina automática que substitua sistemas multi-câmera de 5 – 12 MP (SACMI, Pressco, Keye, etc.), a **VE202G1A não cobre**; você precisará de sensores de maior resolução ou de várias VE-Series trabalhando em conjunto e/ou de deep-learning externo (YOLO + Hailo, por exemplo).

---

## 1. O que realmente entrega a VE202G1A

| Item             | Valor                                                                        | Impacto na aplicação                                                                                                                                 |
| ---------------- | ---------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| Sensor           | 1/1.8" CMOS, 1600 × 1200 px (2 MP), mono                                     | 2 MP limita o campo de visão útil se quiser chegar a < 0,2 mm/defeito ([bannerengineering.com][1], [turckbanner.fr][2])                              |
| FPS              | 50 fps                                                                       | Bom para até ≈ 300 pç/min, mas pré-formas chegam a 700 pç/min em linhas rápidas ([powermatic.net][3])                                                |
| Lentes (C-mount) | 5 – 75 mm; tabela de FOV no manual                                           | Com lente de 16 mm @ 600 mm WD, FOV ≈ 259 mm → 0,162 mm/pixel; seria necessário ≥ 4 px para detectar mancha 0,5 mm ([info.bannerengineering.com][4]) |
| Ferramentas      | Blob, Edge, Medidas, OCR, ID; **sem IA embarcada** (licença “Vision” padrão) | Adequado a defeitos dimensionais simples; falha em detecção complexa de cor/cristalização                                                            |
| I/O & Ethernet   | Ethernet/IP, Profinet, Modbus-TCP; 6 saídas/entradas isoladas                | Integra fácil a CLP, mas não a aceleradores AI                                                                                                       |

### Cálculo rápido de resolução

Para cobrir *todo* o corpo de uma pré-forma padrão 29/25 de 120 mm de comprimento num único disparo:

```
Pixel size = FOV / Npixels
FOV desejado ≈ 120 mm   (altura)
Com lente 12 mm @ 600 mm WD  → 340 mm vertical (tabela)
pixel ≈ 340 / 1200 ≈ 0,283 mm/px
Defeito mínimo (4 px) ≈ 1,13 mm  ➜ acima do limite permitido
```

Mesmo encurtando WD para 150 mm e usando 35 mm, chega-se a ≈ 0,036 mm/px (bom), **mas o FOV cai para 57 mm**, não cabendo a peça inteira: seriam necessárias 3-4 câmeras e sincronismo rotor + eixos.

---

## 2. Requisitos legais e de qualidade no Brasil

| Fonte                                                          | Exigência resumida                                                                                                                                                                          |
| -------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **ABNT NBR 15588:2008** – Pré-forma PET para alimentos/bebidas | Aparência: ausência de pontos pretos > 0,25 mm, bolhas, riscos profundos, opacidade irregular; Dimensões: tolerâncias de gargalo ±0,05 mm; Peso, AA residual, IV, etc. ([target.com.br][5]) |
| **Anvisa RDC 20/2008 + RDC 27/2010**                           | Pré-formas virgens ou PET-PCR grau alimentício exigem rastreabilidade e controle de contaminantes visuais e químicos ([bibliotecadigital.anvisa.gov.br][6])                                 |

Para competitividade em linhas de bebidas (Coca-Cola, Ambev), a prática corrente é detectar pontos pretos ≥ 0,15 mm e bolhas ≥ 0,3 mm a > 600 pç/min.

---

## 3. Pontos críticos onde a VE202G1A fica aquém

1. **Resolução global**: 2 MP obriga a comprometer FOV ou sensibilidade.
2. **Cobertura 360°**: precisa de esteira rotativa ou múltiplas câmeras; uma só não vê costado inteiro.
3. **Profundidade de campo**: gargalo + corpo exigem lente telecêntrica; Banner não oferece versão telecêntrica pronta.
4. **Detecção de cor/cristalização**: modelo é monocromático; versão “C” (color) alivia, mas ainda 2 MP.
5. **IA/Deep Learning**: ausência de motor interno; teria de exportar frames para Raspberry + Hailo rodar YOLO, adicionando latência e cablagem.

---

## 4. Quando ela faz sentido

| Cenário                                                    | Adequação                                        |
| ---------------------------------------------------------- | ------------------------------------------------ |
| **Controle amostral offline** < 120 pç/min                 | ✔ (desde que use lente ≥ 35 mm + backlight)      |
| **Verificação de gargalo/rosca** em linha lenta            | ✔ (área pequena ⇒ boa resolução)                 |
| **Detecção grossa de presença/ausência do “gate”**         | ✔                                                |
| **Inspeção 100 % on-line, defeitos ≥ 0,25 mm, 600 pç/min** | ✖ Necessita multi-câmera 5 – 12 MP               |
| **Sistema baseado em YOLO/Hailo**                          | ✖ (não roda nativamente; só via PC/RPi auxiliar) |

---

## 5. Recomendações práticas

1. **Prova-de-conceito**: coloque uma VE202G1A com lente 35 mm a \~200 mm de WD, backlight vermelho difuso, acione em gatilho de encoder e teste se capta manchas ≥ 0,3 mm em 50 pç/min.
2. **Escalabilidade**: Se precisar 100 % inspeção, considere:

   * VE205G1A 5 MP (mesma série) ou IDS/In-Sight 9902M 12 MP.
   * Configuração em “túnel” com 6 câmeras (3 pares opostos) + disco giratório.
   * Módulo deep-learning: Banner VE-DL (licença) **ou** saída GigE para Hailo-8.
3. **Compliance**: garanta rastreabilidade (armazenar jpg + CSV de resultados), parâmetro já exigido em RDC 20 / NBR 15588.
4. **Ottimização de custo**: se o foco é prototipagem educacional (seu caso Node-RED + Raspberry), pode usar câmera industrial Basler ace 2 8 MP + Hailo + Node-RED para prova e só depois migrar a hardware dedicado.

---

### TL;DR

A **Banner VE202G1A cobre inspeções dimensionais simples e lentas**. Para um sistema de inspeção de pré-formas PET em linha de bebidas conforme NBR 15588 (defeitos < 0,2 mm, \~600 pç/min), ela **não basta sozinha** – será necessário mais resolução, multivisão e/ou IA embarcada. ([bannerengineering.com][1], [turckbanner.fr][2], [powermatic.net][3], [info.bannerengineering.com][4], [target.com.br][5], [bibliotecadigital.anvisa.gov.br][6])

[1]: https://www.bannerengineering.com/us/en/products/part.94843.html?srsltid=AfmBOorXuvq_f4ndt3XZIueCwN2HF_kltICigguqkW384wumhfE4MZ3i&utm_source=chatgpt.com "VE202G1A | VE Smart Camera - Banner Engineering"
[2]: https://www.turckbanner.fr/datasheet/_en/edb_3094843_gbr_en.pdf?utm_source=chatgpt.com "[PDF] VE202G1A Image Processing – Smart Camera - Turck Banner"
[3]: https://www.powermatic.net/part/banner/ve202g1a/1887644?srsltid=AfmBOopTQcMUZxI71mw4uevOLUqMLtz2WpX_xG9Zaz9-6roeDulg_t4p&utm_source=chatgpt.com "VE202G1A Banner - Vision Sensors - Powermatic Associates"
[4]: https://info.bannerengineering.com/cs/groups/public/documents/literature/191666.pdf "VE Series Smart Camera Instruction Manual"
[5]: https://www.target.com.br/produtos/normas-tecnicas/40684/nbr15588-pre-forma-de-pet-para-sopro-de-embalagem-para-alimentos-e-bebidas-requisitos-e-metodos-de-ensaio?utm_source=chatgpt.com "ABNT NBR 15588 NBR15588 Pré-forma de PET para sopro ... - Target"
[6]: https://bibliotecadigital.anvisa.gov.br/jspui/bitstream/anvisa/204/1/ANVISA_Materiais%20em%20contato%20com%20alimentos_perguntas%20e%20respostas_2020.pdf?utm_source=chatgpt.com "[PDF] materiais em contato com alimentos - Vigilância Sanitária"
