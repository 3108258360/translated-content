---
title: "Escape de classes de caracteres unicode: \\p{...}, \\P{...}"
slug: Web/JavaScript/Reference/Regular_expressions/Unicode_character_class_escape
---

{{jsSidebar}}

Um **escape de classe de caracteres unicode** é um tipo de [escape de classe de caracteres](/pt-BR/docs/Web/JavaScript/Reference/Regular_expressions/Character_class_escape) que corresponde a um conjunto de caracteres especificado por uma propriedade Unicode. Este escape é suportado apenas no [modo compatível com Unicode](/pt-BR/docs/Web/JavaScript/Reference/Global_Objects/RegExp/unicode#unicode-aware_mode). Quando o marcador [`v`](/pt-BR/docs/Web/JavaScript/Reference/Global_Objects/RegExp/unicodeSets) está habilitado, também pode ser usado para corresponder com strings de tamanho finito.

{{EmbedInteractiveExample("pages/js/regexp-unicode-property-escapes.html", "taller")}}

## Sintaxe

```regex
\p{propriedadeSolitaria}
\P{propriedadeSolitaria}

\p{propriedade=valor}
\P{propriedade=valor}
```

### Parâmetros

- `propriedadeSolitaria`

  - : Nome ou valor de uma propriedade Unicode solitária, seguindo a mesma sintaxe como `valor`. Ela especifica o valor para a propriedade `General_Category` (Categoria Geral), ou um [nome binário de propriedade (en-US)](https://tc39.es/ecma262/multipage/text-processing.html#table-binary-unicode-properties). No modo [`v`](/pt-BR/docs/Web/JavaScript/Reference/Global_Objects/RegExp/unicodeSets), também pode ser uma [propriedade Unicode binária de strings (en-US)](https://tc39.es/ecma262/multipage/text-processing.html#table-binary-unicode-properties-of-strings).

    > **Nota:** A sintaxe [ICU (en-US)](https://unicode-org.github.io/icu/userguide/strings/unicodeset.html#property-values) permite omitir o nome da propriedade `Script` também, mas o JavaScript não suporta isso, porque na maioria dos casos a propriedade `Script_Extensions` é mais útil que a `Script`.

- `propriedade`
  - : O nome de uma propriedade Unicode. Deve ser composto de letras {{Glossary("ASCII")}} (`A–Z`, `a–z`) e underscores (`_`), e deve ser um dos [nome de propriedades não binárias (en-US)](https://tc39.es/ecma262/multipage/text-processing.html#table-nonbinary-unicode-properties).
- `valor`
  - : O valor de uma propriedade Unicode. Deve ser composto de letras (`A–Z`, `a–z`), underscores (`_`), e dígitos (`0–9`), e deve ser um dos valores suportados listados em [`PropertyValueAliases.txt (en-US)`](https://unicode.org/Public/UCD/latest/ucd/PropertyValueAliases.txt).

## Descrição

`\p` e `\P` são suportados apenas no [modo compatível com Unicode](/pt-BR/docs/Web/JavaScript/Reference/Global_Objects/RegExp/unicode#unicode-aware_mode). No modo compatível com Unicode, eles são [escapes de caracteres](/pt-BR/docs/Web/JavaScript/Reference/Regular_expressions/Character_escape) para o caractere `p` ou `P`.

Todo caracter Unicode possui um conjunto de propriedades que o descreve. Por exemplo, o caracter [`a (en-US)`](https://util.unicode.org/UnicodeJsps/character.jsp?a=0061) possui a propriedade `General_Category` com o valor `Lowercase_Letter`, e a propriedade `Script` com o valor `Latn`. As sequências de escape `\p` e `\P` permitem encontrar caracteres com base em suas propriedades. Por exemplo, `a` pode corresponder a `\p{Lowercase_Letter}` (o nome da propriedade `General_Category` é opcional) assim como `\p{Script=Latn}`. `\P` cria uma _classe complementar_ que consiste de pontos de código sem a propriedade especificada.

Para compor múltiplas propriedades, use a sintaxe de [intersecção de conjunto de caracteres](/pt-BR/docs/Web/JavaScript/Reference/Regular_expressions/Character_class#v-mode_character_class) habilitada com o marcador `v`, ou veja [padrão de subtração e intersecção](/pt-BR/docs/Web/JavaScript/Reference/Regular_expressions/Lookahead_assertion#pattern_subtraction_and_intersection).

No modo `v`, `\p` pode corresponder a uma sequências de pontos de código, definida no Unicode como "propriedades de strings". Isto é mais útil para emojis, que são frequentemente compostos por múltiplos pontos de código. Contudo, `\P` pode apenas complementar as propriedades do caracter.

> **Nota:** Existem planos de portar a funcionalidade de propriedades de string para o modo `u` também.

## Exemplos

### Categorias gerais

Categorias gerais são utilizada para classificar caracteres Unicode e subcategorias estão disponíveis para definir uma categorização mais precisa. É possível utilizar ambas as formas, curta ou longa, no escape de uma propriedade Unicode.

Elas podem ser utilizadas para corresponder a letras, números, símbolos, pontuações, espaços, etc. Para uma lista mais extensa de categorias gerais, por favor consulte [a especificação Unicode (en-US)](https://unicode.org/reports/tr18/#General_Category_Property).

```js
// encontrando todas as letras de um texto
const story = "É o Gato Listrado: agora eu deve ter alguém com quem conversar.";

// Forma mais explícita
story.match(/\p{General_Category=Letter}/gu);

// Não é obrigatório usar o nome da propriedade para categorias gerais
story.match(/\p{Letter}/gu);

// Isso é equivalente (forma curta):
story.match(/\p{L}/gu);

// Isso também é equivalente (conjunção de todas as subcategorias usando a forma curta)
story.match(/\p{Lu}|\p{Ll}|\p{Lt}|\p{Lm}|\p{Lo}/gu);
```

### Escritas e extensões de escritas

Algumas linguagens utilizam escritas diferentes para escrever seus sistemas. Por exemplo, inglês e espanhol são escritos utilizando latim enquanto árabe e russo são escritos utilizando outras escritas (respectivamente árabe e cirílico). As propriedades Unicode `Script` e `Script_Extensions` permitem que expressões regulares correspondam com caracteres de acordo com a escrita com a qual elas são amplamente utilizadas (`Script`) ou de acordo com o conjunto de escritas as quais elas pertencem (`Script_Extensions`).

Por exemplo, `A` pertence a escrita `Latin` (latim) e `ε` a escrita `Greek` (Grega).

```js
const caracteresMisturados = "aεЛ";

// Utilizando o canonico nome "longo" da escrita
caracteresMisturados.match(/\p{Script=Latin}/u); // a

// Utilizando a forma curta (código ISO 15924) para a escrita
caracteresMisturados.match(/\p{Script=Grek}/u); // ε

// Utilizando o nome curto sc para a propriedade Script
caracteresMisturados.match(/\p{sc=Cyrillic}/u); // Л
```

Para mais detalhes, consulte [a especificação Unicode (en-US)](https://unicode.org/reports/tr24/#Script), a [especificação ECMAScript da tabela de escritas (en-US)](https://tc39.es/ecma262/multipage/text-processing.html#table-unicode-script-values), e a [lista de códigos de escritas ISO 15924 (en-US)](https://unicode.org/iso15924/iso15924-codes.html).

Se o caracter é utilizado em um conjunto limitado de escritas, a propriedade `Script` vai apenas combinar para a escrita "predominantemente" utilizada. Se nós queremos que os caracteres correspondam com base na escrita "não predominante", nós podemos utilizar a propriedade `Script_Extensions` (`Scx` para a forma curta).

```js
// ٢ é o dígito 2 na notação Árabe-Índico
// enquanto ela é predominantemente escrita com a escrita Árabe
// ela ainda pode ser escrita na escrita Thaana

"٢".match(/\p{Script=Thaana}/u);
// null já que Thaana não é a escrita predominante

"٢".match(/\p{Script_Extensions=Thaana}/u);
// ["٢", index: 0, input: "٢", groups: undefined]
```

### Escape de propriedades Unicode vs. Classes de caracteres

With JavaScript regular expressions, it is also possible to use [character classes](/pt-BR/docs/Web/JavaScript/Guide/Regular_expressions/Character_classes) and especially `\w` or `\d` to match letters or digits. However, such forms only match characters from the _Latin_ script (in other words, `a` to `z` and `A` to `Z` for `\w` and `0` to `9` for `\d`). As shown in [this example](/pt-BR/docs/Web/JavaScript/Guide/Regular_expressions/Character_classes#looking_for_a_word_from_unicode_characters), it might be a bit clumsy to work with non Latin texts.

Unicode property escapes categories encompass much more characters and `\p{Letter}` or `\p{Number}` will work for any script.

```js
// Trying to use ranges to avoid \w limitations:

const nonEnglishText = "Приключения Алисы в Стране чудес";
const regexpBMPWord = /([\u0000-\u0019\u0021-\uFFFF])+/gu;
// BMP goes through U+0000 to U+FFFF but space is U+0020

console.table(nonEnglishText.match(regexpBMPWord));

// Using Unicode property escapes instead
const regexpUPE = /\p{L}+/gu;
console.table(nonEnglishText.match(regexpUPE));
```

### Matching prices

The following example matches prices in a string:

```js
function getPrices(str) {
  // Sc stands for "currency symbol"
  return [...str.matchAll(/\p{Sc}\s*[\d.,]+/gu)].map((match) => match[0]);
}

const str = `California rolls $6.99
Crunchy rolls $8.49
Shrimp tempura $10.99`;
console.log(getPrices(str)); // ["$6.99", "$8.49", "$10.99"]

const str2 = `US store $19.99
Europe store €18.99
Japan store ¥2000`;
console.log(getPrices(str2)); // ["$19.99", "€18.99", "¥2000"]
```

### Matching strings

With the `v` flag, `\p{…}` can match strings that are potentially longer than one character by using a property of strings:

```js
const flag = "🇺🇳";
console.log(flag.length); // 2
console.log(/\p{RGI_Emoji_Flag_Sequence}/v.exec(flag)); // [ '🇺🇳' ]
```

However, you can't use `\P` to match "a string that does not have a property", because it's unclear how many characters should be consumed.

```js-nolint example-bad
/\P{RGI_Emoji_Flag_Sequence}/v; // SyntaxError: Invalid regular expression: Invalid property name
```

## Specifications

{{Specifications}}

## Browser compatibility

{{Compat}}

## See also

- [Character classes](/pt-BR/docs/Web/JavaScript/Guide/Regular_expressions/Character_classes) guide
- [Regular expressions](/pt-BR/docs/Web/JavaScript/Reference/Regular_expressions)
- [Character class: `[...]`, `[^...]`](/pt-BR/docs/Web/JavaScript/Reference/Regular_expressions/Character_class)
- [Character class escape: `\d`, `\D`, `\w`, `\W`, `\s`, `\S`](/pt-BR/docs/Web/JavaScript/Reference/Regular_expressions/Character_class_escape)
- [Character escape: `\n`, `\u{...}`](/pt-BR/docs/Web/JavaScript/Reference/Regular_expressions/Character_escape)
- [Disjunction: `|`](/pt-BR/docs/Web/JavaScript/Reference/Regular_expressions/Disjunction)
- [Unicode character property](https://en.wikipedia.org/wiki/Unicode_character_property) on Wikipedia
- [ES2018: RegExp Unicode property escapes](https://2ality.com/2017/07/regexp-unicode-property-escapes.html) by Dr. Axel Rauschmayer (2017)
- [Unicode regular expressions § Properties](https://unicode.org/reports/tr18/#Categories)
- [Unicode Utilities: UnicodeSet](https://util.unicode.org/UnicodeJsps/list-unicodeset.jsp)
- [RegExp v flag with set notation and properties of strings](https://v8.dev/features/regexp-v-flag) on v8.dev (2022)
