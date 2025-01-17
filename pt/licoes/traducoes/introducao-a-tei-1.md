---
title: Introdução à codificação de texto em TEI (parte 1)
collection: lessons
layout: lesson
slug: introducao-a-tei-1
authors:
  - Nicolás Vaughan
editors:
  - Jennifer Isasi
reviewers:
  - Rocío Méndez
  - Iñaki Cano
review-ticket: https://github.com/programminghistorian/ph-submissions/issues/366
translators:
  - Nicolás Llano
difficulty: 2
activity: transformimg
topics:
- data-manipulation
- website
abstract: "Esta lição (a primeira de duas partes) ensina você as bases de TEI-XML para codificar textos."
doi: 10.46430/phes0053
---

{% include toc.html %}

# Introdução

Um dos problemas centrais das humanidades digitais tem sido o trabalho com e sobre textos: a sua captura (digitalização), reconhecimento, transcrição, codificação, processamento, transformação e análise.
Nesta lição nos centraremos exclusivamente na codificação de textos, ou seja, na sua categorização por meio de etiquetas (tags).

Um exemplo pode ajudar a esclarecer esta ideia. Vamos supor que temos um documento impresso já digitalizado anteriormente.
Temos as imagens das páginas digitalizadas e, com a ajuda de um software de reconhecimento óptico de caracteres ([OCR](https://pt.wikipedia.org/wiki/Reconhecimento_%C3%B3tico_de_caracteres), em inglês), extraímos o texto contido nas páginas. Este texto é  chamado [texto simples](https://www.ecured.cu/Texto_plano) (ou texto digitalizado), isto é, o texto sem nenhum formato (sem cursiva, negritas, etc.) nem nenhuma outra estruturação semântica.

Embora pareça estranho, o texto simples carece completamente de conteúdo. Para um computador é só uma longa cadeia de caracteres (incluindo a pontuação, espaços em branco e saltos de linha, etc.) em alguma [codificação](https://pt.wikipedia.org/wiki/Codifica%C3%A7%C3%A3o_de_caracteres) (por exemplo [UTF-8](https://pt.wikipedia.org/wiki/UTF-8) ou [ASCII](https://pt.wikipedia.org/wiki/ASCII)) de algum alfabeto (por exemplo latim, grego ou cirílico). Somos nós quem, quando lemos, identificamos palavras (em uma ou várias línguas), linhas, parágrafos, etc. Somos nós também que identificamos nomes de pessoas e lugares, títulos de livros e artigos, datas, citações, títulos, referências cruzadas (internas e externas), notas de rodapé e notas adicionais no final do texto. Mas, mais uma vez, o computador é completamente “ignorante” de tais estruturas textuais em um texto simples sem processamento ou codificação.

Sem assistência humana, por exemplo, através da codificação [TEI](https://tei-c.org/) (Text Encoding Initiative), o computador não pode “entender” ou detectar nenhum conteúdo no texto simples. Isto significa, entre outras coisas, que não podemos fazer buscas estruturadas naquele texto (por exemplo, por nomes de pessoas, lugares e datas), nem podemos extrair e processar sistematicamente aquela informação, sem antes indicar ao computador quais cadeias de caracteres correspondem a quais estruturas semânticas: por exemplo, que esse é o nome próprio de uma pessoa, que o nome de outra pessoa se refere à mesma pessoa, que esse é um nome de lugar, que essa é uma nota na margem feita por uma terceira pessoa, ou que este parágrafo pertence a tal seção do texto.

Codificar o texto é indicar (por meio de etiquetas e outros recursos) que certas cadeias de texto simples têm um certo significado.

E essa é a diferença entre texto simples e texto estruturado semanticamente.

Há muitas maneiras de codificar um texto. Por exemplo, podemos incluir os nomes próprios das pessoas entre asteriscos simples: `*Fernando Pessoa*`, `*Carolina Maria de Jesus*`, etc. E entre asteriscos duplos para os lugares: `**Salvador**`, `**Lisboa**`, etc. Também podemos utilizar _____ para indicar os nomes de obras e livros: `__A Divina Comédia_`, `_Memórias Pósthumas de Braz Cubas_`, etc. Estes signos servem para etiquetar ou marcar o texto que ___ encerram, de modo a identificar um determinado conteúdo no texto. Como você pode facilmente imaginar, as possibilidades de codificação são quase infinitas.

Nesta lição, você aprenderá como codificar textos usando uma linguagem de computador especialmente desenhada para isso: TEI.

## O software que usaremos

Qualquer editor de texto simples (em formato `.txt`) fará tudo o que precisamos fazer nesta lição: Windows [Notepad](https://pt.wikipedia.org/wiki/Bloco_de_Notas), por exemplo, é perfeitamente adequado para isto. Entretanto, há outros editores de texto que oferecem ferramentas ou funcionalidades destinadas a facilitar o trabalho com XML (Extensible Markup Language) e até mesmo com TEI. Um dos mais recomendados atualmente é [Oxygen XML Editor](https://www.oxygenxml.com), disponível para Windows, MacOS e Linux. Entretanto, não é um software livre (a licença acadêmica custa cerca de US$ 100 USD) nem tem código aberto, portanto não o utilizaremos nesta lição.

Para esta lição usaremos o editor [Visual Studio Code](https://code.visualstudio.com/) (VS Code, mais brevemente), criado pela Microsoft e atualmente mantido por uma grande comunidade de programadores de código aberto. É uma aplicação totalmente gratuita e de [código aberto] (https://github.com/microsoft/vscode), disponível para Windows, MacOS e Linux.

Baixe a última versão do Código VS no link [https://code.visualstudio.com/](https://code.visualstudio.com/) e instale-o em seu computador. Agora abra o software e você verá uma tela parecida com a seguinte imagem:

{% include figure.html filename="introducao-a-tei-1-01.png" caption="Vista inicial do VS Code" %}

Agora vamos instalar uma extensão do Código VS chamada [XML Complete](https://marketplace.visualstudio.com/items?itemName=rogalmic.vscode-xml-complete), para facilitar o trabalho com documentos XML. Para fazer isso, clique no botão de extensão na barra de ferramentas lateral do lado esquerdo da janela principal:

{% include figure.html filename="introducao-a-tei-1-02.png" caption="Extensões de VS Code" %}

Digite `Xml completo` no campo de busca:

{% include figure.html filename="introducao-a-tei-1-03.png" caption="Busca de extensões de VS Code" %}

Finalmente, clique em "Instalar":

{% include figure.html filename="introducao-a-tei-1-04.png" caption="Instalar \"XML Complete\" no VS Code" %}

A extensão completa do XML nos permite, entre outras coisas, validar formalmente os documentos XML. Se houver um erro formal -por exemplo, se esquecemos de fechar uma etiqueta- o Código VS irá mostrá-lo na barra inferior:

{% include figure.html filename="introducao-a-tei-1-05.png" caption="Identificar erros sintáticos em VS Code" %}

{% include figure.html filename="introducao-a-tei-1-06.png" caption="Identificar erros sintáticos em VS Code (detalhe)" %}

Esta extensão também oferece outras ferramentas úteis para auto-completar o código XML. Para mais detalhes, consulte a [documentação](https://github.com/rogalmic/vscode-xml-complete) (unicamente disponível em inglês).

## Visualização vs. categorização

Aqueles familiarizados com a linguagem de marcação [Markdown](https://daringfireball.net/projects/markdown/syntax) - habitual na atualidade em fóruns técnicos na Internet, assim como em [GitHub](https://github.com), [GitLab](https://gitlab.com) e outros repositórios de código - certamente reconhecerão o uso de elementos como asteriscos (`*`), sublinhados (`_`) e hashtag (`#`) para fazer com que o texto apareça de certa forma no navegador. Por exemplo, o texto incluído em asteriscos simples será mostrado em itálico e o texto incluído em asteriscos duplos será mostrado em negrito. Inclusive, o texto desta lição está escrito em Markdown, seguindo estas convenções.

Este uso da marcação tem como objetivo principal visualizar o texto, não sua categorização. Em outras palavras, as marcas ou etiquetas de Markdown não indicam que um texto seja de certa categoria (por exemplo, o nome de uma pessoa, de um lugar ou de uma obra), mas apenas que o texto deve ser exibido ou mostrado de certa forma em um navegador ou outra mídia.

Compreender a diferença entre a marcação de visualização (como o de Markdown) e a marcação semântica (ou estrutural, como veremos mais adiante em TEI) é crucial para entender o propósito da codificação de textos. Quando marcamos um trecho de texto para codificá-lo, o fazemos sem nos preocuparmos, em princípio, com a forma em que  foi originalmente representado ou como poderá ser eventualmente representado no futuro. Estamos interessados apenas na função semântica ou estrutural que um texto em particular tem. É por isso que devemos tentar identificar precisamente as funções ou categorias dos textos, deixando de lado na medida do possível, a forma como são exibidos no papel ou na tela.

Para esclarecer este ponto, voltemos ao nosso exemplo inicial. Vamos supor que no texto digitalizado a partir com o qual começamos os nomes próprios sempre aparecem impressos em [small caps](https://pt.wikipedia.org/wiki/Versalete), como no fragmento a seguir:

{% include figure.html filename="introducao-a-tei-1-07.png" caption="Fragmento curto  de texto digitalizado de _Memórias Pósthumas de Braz Cubas_" %}

Como veremos mais adiante, o TEI nos permite codificar, por meio de uma série de etiquetas, o texto que queremos categorizar. Por exemplo, podemos utilizar uma etiqueta como [`<name>`](https://tei-c.org/release/doc/tei-p5-doc/en/html/ref-name.html) para demarcar os nomes próprios contidos no texto, como este:

```XML
Vem cá, <name>Eugenia</name>, disse ella, comprimenta o <name>Dr. Braz Cubas</name>, filho do <name>Sr. Cubas</name>; veiu da Europa.
```

Mais adiante veremos em detalhe o que é uma etiqueta (ou mais precisamente um elemento) e como ela funciona em XML e TEI. Por enquanto, note-se que a etiqueta não significa que o texto tenha sido originalmente representado em letra versalete (ou de qualquer outra forma). Isso apenas significa que o texto contido tem o status de um nome próprio, independentemente de como ele seja representado. É mais, podemos codificar exaustivamente um documento com centenas ou milhares de etiquetas, sem que nenhuma delas apareça no final em uma eventual representação.

# XML e TEI: para um padrão de codificação de textos

Desde o início das humanidades digitais nos anos 60, houve muitas abordagens à codificação de textos. Quase todo projeto de codificação tinha seu próprio padrão, o que levou a ter projetos incompatíveis e intraduzíveis uns com os outros, dificultando e até mesmo impossibilitando o trabalho colaborativo.

Para resolver este problema, quase vinte anos depois foi estabelecido um novo padrão de codificação de texto, concertado por um grande número de pesquisadores de todo o mundo, especialmente de universidades anglo-saxônicas: a [Text Encoding Initiative (TEI)] (https://es.wikipedia.org/wiki/Text_Encoding_Initiative).

O próprio TEI é construída sobre a linguagem de marcação [XML] (https://pt.wikipedia.org/wiki/XML) e é, portanto, às vezes referida como "TEI-XML" (ou também "XML/TEI"). XML (que significa "eXtensible Markup Language") é uma linguagem computacional cujo objetivo é descrever, por meio de uma série de marcações ou etiquetas (_tags_ em inglês), um determinado objeto de texto.
XML e TEI são linguagens de marcação e nesse sentido se diferençam das linguagens de programação como C, Python ou Java, que descrevem objetos, funções ou procedimentos a serem executados por um computador.

## XML

Nesta lição, não entraremos em detalhes sobre a sintaxe e o funcionamento do XML. Recomendamos, portanto, que o leitor dê uma olhada em (/en/lessons/data-transformation-xml-xsl) para mais informações sobre XML, assim como a bibliografia e as referências sugeridas ao final desta lição.

Por enquanto, só precisamos saber que cada documento XML deve obedecer a duas regras básicas para ser válido:

1. Deve existir apenas um elemento raiz (que contenha todos os outros elementos, se houver).
2. Cada etiqueta de abertura deve ter uma etiqueta de fechamento.

## Validação sintáctica de documentos XML

Podemos descobrir se um documento XML é sintaticamente válido com a ajuda de nosso editor de texto (Código VS com a extensão XML Complete). Também é possível encontrar aplicações de validação gratuitas na Internet, por exemplo (https://codebeautify.org/xmlvalidator)[https://codebeautify.org/xmlvalidator] ou (https://www.xmlvalidation.com)[https://www.xmlvalidation.com].

Se copiarmos e colarmos o último exemplo neste último link (ou se carregarmos o arquivo correspondente), teremos o seguinte erro:

{% include figure.html filename="introducao-a-tei-1-08.png" caption="Validação online do último exemplo" %}

## O que é o TEI?

O XML é uma linguagem tão geral e abstrata que é totalmente indiferente ao seu conteúdo. Pode ser usado, por exemplo, para descrever coisas tão divergentes como um texto grego clássico do século VIII a.C. e a mensagem que um termostato inteligente envia para um aplicativo de um smartphone para controlá-lo.

O TEI é uma implementação particular da XML. Ou seja, é um conjunto de regras que determinam quais elementos e quais atributos são permitidos em um documento de um determinado tipo. Mais precisamente, o TEI é uma linguagem de marcação para codificar textos de todo o tipo. Isto é com o objetivo de que possam ser processados por um computador, para que possam ser analisados, transformados, reproduzidos e armazenados, dependendo das necessidades e interesses dos usuários (tanto os de carne e osso como os computacionais). É por isso que podemos dizer que o TEI está no coração das humanidades digitais (ou pelo menos de um de seus corações!). É um padrão de trabalho computacional com uma classe de objetos tradicionalmente central para as humanidades: os textos. Assim, enquanto o XML não se importa se os elementos em um documento descrevem textos (ou as propriedades do texto), o TEI foi desenhado exclusivamente para trabalhar com eles.

Os tipos de elementos e atributos permitidos no TEI, e as relações entre eles, são especificados pelas [regras do TEI] (https://tei-c.org/release/doc/tei-p5-doc/en/html/index.html). Por exemplo, se quisermos codificar um poema, podemos utilizar o elemento [`<lg>`](https://tei-c.org/release/doc/tei-p5-doc/en/html/ref-lg.html) (de _ grupo de linhas_, "grupo de linhas") do TEI. As regras do TEI determinam que tipos de atributos esse elemento pode ter e que elementos podem, por sua vez, conter ou ser contidos por ele. O TEI determina que cada elemento `<lg>` deve ter pelo menos um elemento [`<l>`](https://tei-c.org/release/doc/tei-p5-doc/en/html/ref-l.html) (de _line_, "linha").

Como ilustração, examinemos as primeiras três linhas do soneto *Hiato* de Manuel Bandeira (abaixo em texto simples):

```XML
És na minha vida como um luminoso
Poema que se lê comovidamente
Entre sorrisos e lágrimas de gozo...
```

Podemos propor a seguinte codificação no TEI:

```XML
<lg met="11,11,11" rhyme="abba">
 <l n="1">És na minha vida como um luminoso</l>
 <l n="2">Poema que se lê comovidamente/l>
 <l n="3">Entre sorrisos e lágrimas de gozo...</l>
</lg>
```

Neste caso, utilizamos o atributo `@rhyme` do elemento `<lg>` para codificar o tipo de rima do trecho; o atributo `@met` para indicar o tipo de métrica da primeira linha (hendecassílabo) (isto teria que ser feito em cada uma das linhas, embora para clareza do código o tenhamos feito apenas na primeira); e finalmente o atributo `@n` para indicar o número da linha dentro do grupo.

Comparar o texto simples do fragmento do soneto com sua codificação nos permite começar a ver as vantagens do TEI como uma linguagem de marcação de textos. Não somente fica explicitamente declarado que da segunda a quarta linha (no código acima) são linhas de um poema, mas que elas têm um tipo de rima e métrica. Uma vez codificado todo o poema, ou todos os poemas de uma coleção, podemos, por exemplo, usar um software para realizar buscas estruturadas, de modo que ele retorne todos os poemas com um certo tipo de rima ou todas as linhas que têm um certo tipo de métrica. Ou podemos usar (ou criar) um aplicativo para determinar quantas linhas dos sonetos de Quevedo -se houver- têm métrica imperfeita. Também podemos comparar as diferentes versões (os "testemunhadores" e as "testemunhas" manuscritas e impressas) dos sonetos, visando fazer uma edição crítica dos mesmos.

Agora, tudo isso e muito mais só é possível em virtude do fato de termos explicitado, graças à TEI, o conteúdo desses sonetos. Se tivéssemos apenas o texto simples deles, seria tecnicamente impossível tirar proveito das ferramentas computacionais projetadas para editá-los, transformá-los, visualizá-los, analisá-los ou publicá-los.

# Um documento mínimo de TEI

Examinemos agora o seguinte documento mínimo de TEI:
```XML
<?xml version="1.0" encoding="UTF-8"?>
<TEI xmlns="http://www.tei-c.org/ns/1.0">
  <teiHeader>
      <fileDesc>
         <titleStmt>
            <title>Título</title>
         </titleStmt>
         <publicationStmt>
            <p>Informação da publicação</p>
         </publicationStmt>
         <sourceDesc>
            <p>Informação sobre a fonte</p>
         </sourceDesc>
      </fileDesc>
  </teiHeader>
  <text>
      <body>
         <p>Algum texto...</p>
      </body>
  </text>
</TEI>
```

A primeira linha é a já tradicional declaração do documento XML.
A segunda linha contém o elemento principal ou "raiz" deste documento: o elemento `<TEI>`. O atributo `@xmlns` com o valor `http://www.tei-c.org/ns/1.0` simplesmente declara que todos os elementos e atributos filhos do elemento `<TEI>` pertencem ao "namespace" do TEI (representado aqui pela URL). Não teremos que nos preocupar mais com isso a partir de agora.

O interessante vem em seguida nas linhas três e dezesseis, que contêm respectivamente os dois filhos imediatos do elemento raiz:

- [`<teiHeader>`](https://tei-c.org/release/doc/tei-p5-doc/en/html/ref-teiHeader.html)
- [`<text>`](https://tei-c.org/release/doc/tei-p5-doc/en/html/ref-text.html)

Vejamos agora em que consistem estes dois elementos.

## O elemento <teiHeader>

Todos os metadados do documento estão codificados no elemento `<teiHeader>': o título, autoras ou autores, onde, quando e como foi publicado, sua fonte, de onde a fonte foi retirada, e assim por diante. É comum que as pessoas que começam a codificar texto com TEI passem por cima destas informações, preenchendo estes campos com dados genéricos e incompletos. No entanto, as informações no `<teiHeader>` são essenciais para a tarefa do codificador, pois servem para identificar com precisão o texto codificado.

O `<teiHeader>` deve conter pelo menos um elemento chamado `<fileDesc>` (de_file description_ ou descrição do arquivo) que, por sua vez, contém três elementos filhos:

- [`<titleStmt>`](https://tei-c.org/release/doc/tei-p5-doc/en/html/ref-titleStmt.html) (de _title statement_ o enunciado de título): a informação sobre o título do documento (dentro do elemento [`<title>`](https://tei-c.org/release/doc/tei-p5-doc/en/html/ref-title.html)); opcionalmente também pode incluir dados sobre as autoras ou autores (dentro do elemento [`<author>`](https://tei-c.org/release/doc/tei-p5-doc/en/html/ref-author.html))
- [`<publicationStmt>`](https://tei-c.org/release/doc/tei-p5-doc/en/html/ref-publicationStmt.html) (de _publication statement_ o enunciado de publicação): a informação sobre como o documento é publicado ou disponível (ou seja, o próprio documento TEI, não sua fonte). Nesse sentido, é análogo às informações da editora/impressora na folha de rostro de um livro. Pode ser um parágrafo descritivo (dentro do elemento de parágrafo genérico [`<p>`](https://tei-c.org/release/doc/tei-p5-doc/en/html/ref-p.html)) ou pode estar estruturada em um ou vários campos dentro dos seguintes elementos:
  - [`<address>`](https://tei-c.org/release/doc/tei-p5-doc/en/html/ref-address.html): o endereço postal de quem edita/codifica
  - [`<date>`](https://tei-c.org/release/doc/tei-p5-doc/en/html/ref-date.html): a data de publicação do documento
  - [`<pubPlace>`](https://tei-c.org/release/doc/tei-p5-doc/en/html/ref-pubPlace.html): o local de publicação do documento
  - [`<publisher>`](https://tei-c.org/release/doc/tei-p5-doc/en/html/ref-publisher.html): a pessoa que edita/codifica o documento
  - [`<ref>`](https://tei-c.org/release/doc/tei-p5-doc/en/html/ref-ref.html) (ou também [`<ptr>`](https://tei-c.org/release/doc/tei-p5-doc/en/html/ref-ptr.html)): uma referência externa (URL) onde fica disponível o documento
 - [`<sourceDesc>`](https://tei-c.org/release/doc/tei-p5-doc/en/html/ref-sourceDesc.html) (de _source description_ ou descrição da fonte): a informação sobre a fonte da qual é tirado o texto que está sendo codificado. Pode ser um parágrafo descritivo (dentro do elemento de parágrafo genérico, `<p>`). Também pode ser estruturada de várias maneiras. Por exemplo, ele pode ter um elemento [`<bibl>`](https://tei-c.org/release/doc/tei-p5-doc/en/html/ref-bibl.html), que inclui uma referência bibliográfica não-estruturada (p. ej. `<bibl>Machado de Assis, "Memórias Pósthumas de Braz Cubas", Rio de Janeiro: Typographia Nacional , 1881</bibl>`); ou pode conter uma referência estruturada do elemento [`<biblStruct>`](https://tei-c.org/release/doc/tei-p5-doc/en/html/ref-biblStruct.html) que, por sua vez, contém outros elementos relevantes

Suponha que queremos codificar as *Memórias Pósthumas de Braz Cubas* de Machado de Assis, a partir dessa [edição](https://www.gutenberg.org/ebooks/54829) disponível gratuitamente no [Gutenberg Project](https://www.gutenberg.org). O `<teiHeader>` de nosso documento TEI poderia muito bem ser o seguinte:

```XML
<teiHeader>
  <fileDesc>
    <titleStmt>
      <title>Memórias Pósthumas de Braz Cubas</title>
      <author>Machado de Assis</author>
    </titleStmt>
    <publicationStmt>
      <p>
        Codificado em TEI por Nicolás Vaughan em junho de 2021.
        Disponível em https://github.com/nivaca/quijoteuno
      </p>
    </publicationStmt>
    <sourceDesc>
      <p>
        Texto retirado de:
        Machado de Assis, "Memórias Pósthumas de Braz Cubas". Rio de Janeiro: Typographia Nacional, 1881.
        Disponível aqui: https://www.gutenberg.org/ebooks/54829
      </p>
    </sourceDesc>
  </fileDesc>
</teiHeader>
```

Esta é a informação mínima para identificar o documento codificado. Ela nos informa o título e o autor do texto, a pessoa responsável pela codificação e a fonte da qual o texto foi tirado.

Entretanto, é possível -e às vezes desejável- especificar os metadados do documento com mais detalhes. Por exemplo, considere esta outra versão do `<<teiHeader>` para o mesmo texto:

```XML
<teiHeader>
  <fileDesc>
    <titleStmt>
      <title>Memórias Pósthumas de Braz Cubas</title>
      <author>Machado de Assis</author>
    </titleStmt>
    <publicationStmt>
      <publisher>Nicolás Vaughan</publisher>
      <pubPlace>Bogotá, Colômbia</pubPlace>
      <date>2021</date>
      <availability>
        <p>Esta es una obra de acceso abierto licenciada bajo una licencia Creative Commons Attribution 4.0 International.</p>
      </availability>
      <ref target="https://github.com/nivaca/quijoteuno"/>
    </publicationStmt>
    <sourceDesc>
      <biblStruct>
        <monogr>
          <author>Machado de Assis</author>
          <editor></editor>
          <title>Memórias Pósthumas de Braz Cubas</title>
          <edition>1</edition>
          <imprint>
            <publisher>Typographia Nacional</publisher>
            <pubPlace>Rio de Janeiro</pubPlace>
            <date>1881</date>
          </imprint>
        </monogr>
        <ref target="https://www.gutenberg.org/ebooks/54829"/>
      </biblStruct>
    </sourceDesc>
  </fileDesc>
</teiHeader>
```

A decisão sobre a abrangência das informações no `<teiHeader>` depende de sua disponibilidade, e atende os propósitos de codificação e os interesses do codificador/editor. Agora bem, mesmo que os metadados contidos no `<teiHeader>` de um documento TEI não necessariamente apareçam literalmente no texto codificado, não significa que sejam irrelevantes para o processo de codificação, edição e eventual transformação. Na realidade, enquanto um `<teiHeader>` seja codificado de forma correta e exaustiva, as informações contidas no documento poderão ser extraídas e transformadas.

Por exemplo, se fosse importante para nós distinguirmos entre as diferentes edições e impressões do *Quixote*, as informações dos diferentes documentos transcritos contidas no `<teiHeader>` seriam suficientes para poder discriminar entre eles automaticamente. Na realidade, poderíamos aproveitar os elementos `<edição>` e `<impressão>` para esse fim, e com a ajuda de tecnologias como [XSLT](https://www.w3.org/TR/xslt/), [XPath](https://www.w3.org/TR/xpath/) e [XQuery](https://www.w3.org/TR/xquery/) poderíamos localizar, extrair e processar toda a informação.

Em conclusão, quanto mais completa e minuciosamente os metadados dos textos forem codificados no `<teiHeader>` de nossos documentos TEI, mais controle teremos sobre sua identidade e natureza.

## O elemento <text>

Como vimos acima no documento mínimo, `<text>` é o segundo filho de `<TEI>`. Ele contém todo o texto do documento, propriamente dito. Conforme a [documentação TEI](https://tei-c.org/guidelines/p5/), o `<text>` pode conter vários elementos nos quais o texto objeto vai ser estruturado:

{% include figure.html filename="introducao-a-tei-1-09.png" caption="Elementos possíveis de `<text>`" %}

O mais importante destes elementos é [`<body>`](https://tei-c.org/release/doc/tei-p5-doc/en/html/ref-body.html), que contém o corpo principal do texto. Entretanto, outros elementos importantes filhos de `<text>` são [`<front>`](https://tei-c.org/release/doc/tei-p5-doc/en/html/ref-front.html), que contém o _frontmatter_ (páginas preliminares) de um texto (introdução, prefácio, etc.), e [`<back>`](https://tei-c.org/release/doc/tei-p5-doc/en/html/ref-back.html), que contém o _backmatter_ (páginas finais, anexos, índices, etc.).

O elemento `<body>` pode, por sua vez, conter muitos outros elementos:

{% include figure.html filename="introducao-a-tei-1-10.png" caption="Elementos possíveis de `<body>`" %}

Embora que todas essas possibilidades possam nos sobrecarregar a primeira vista, devemos lembrar que normalmente um texto é dividido naturalmente em secções ou partes constitutivas. É recomendável, dessa maneira, usar o elemento [`<div>`](https://tei-c.org/release/doc/tei-p5-doc/en/html/ref-div.html) para cada uma das partes e usar atributos como `@type` ou `@n` para qualificar suas diferentes classes e posições no texto (p. ej. `<div n="3" type="subsecção">...</div>`).

Se nosso texto for curto ou simples, podemos usar um só `<div>` unicamente. Por exemplo:

```XML
<text>
  <body>
    <div>
      <!-- aqui ficaria todo nosso texto -->
    </div>
  </body>
</text>
```

Mas se nosso texto for mais complexo, utilizaremos vários elementos `<div>`:

```XML
<text>
  <body>
    <div>
      <!-- aqui ficaria a primeira secção ou divisão  -->
    </div>
    <div>
      <!-- aqui ficaria a segunda secção ou divisão -->
    </div>
    <!-- etc. -->
  </body>
</text>
```

A estrutura do nosso documento TEI deve, em princípio, ter semelhança com a estrutura do texto objeto, isto é, o texto que queremos codificar. Dessa forma, se nosso objeto está dividido em capítulo, e estes se dividem em seções ou apartados, e por sua vez, estes em parágrafos, o recomendável é que repliquemos essa mesma estrutura no documento TEI.

Para os capítulos e secções podemos usar o elemento `<div>` e para os parágrafos o elemento [`<p>`](https://tei-c.org/release/doc/tei-p5-doc/en/html/ref-p.html).
Vejamos, por exemplo, o seguinte esquema:

```XML
<text>
  <body>
    <div type="capítulo" n="1">
      <!-- este é o primeiro capítulo -->
      <div type="secção" n="1">
        <!-- esta é a primeira secção -->
        <p>
          <!-- este é o primeiro parágrafo -->
        </p>
        <p>
          <!-- este é o segundo parágrafo -->
        </p>
        <!-- ... -->
      </div>
    </div>
    <!-- ... -->
  </body>
</text>
```

Embora o TEI nos permita codificar exaustivamente muitos dos aspectos e propriedades de um texto, às vezes não estamos interessados em todos eles. Além disso, o processo de codificação pode ser desnecessariamente demorado se codificarmos elementos dos quais nunca tiraremos vantagem em uma eventual transformação. Por exemplo, se estamos codificando o texto de uma edição impressa, pode acontecer que as divisões de linha nos parágrafos não sejam relevantes para nossa codificação.

Nesse caso, podemos ignorá-las e simplesmente ficar com as divisões de parágrafos, sem ir além delas. Ou podemos ser tentados a codificar sistematicamente todas as datas e nomes de lugares (com os elementos [`<date>`](https://tei-c.org/release/doc/tei-p5-doc/en/html/ref-date.html) e [`<placeName>`](https://tei-c.org/release/doc/tei-p5-doc/en/html/ref-placeName.html), respectivamente) que aparecem em nosso texto objeto, mesmo que nunca os utilizemos mais tarde. Fazer isso não é um erro, é claro, mas podemos perder tempo valioso ao fazê-lo.

Em resumo, poderíamos formular a "regra de ouro" da codificação da seguinte forma: codificar todos e somente aqueles elementos que possuem um certo significado para nós, considerando que eventualmente poderemos usá-los para fins específicos.

## Conclusões
Nesta primeira parte da lição, você aprendeu:

1. Oque significa codificar um texto
2. O que são documentos XML e XML-TEI?

Na segunda parte, a ser publicada em breve, você verá em detalhe dois exemplos de codificação de texto.

## Referências recomendadas

- A documentação completa do TEI (as *TEI Guidelines*) está disponível em [o site do consórcio](https://tei-c.org/guidelines/). Embora esteja disponível em vários idiomas, só está completa em inglês.

- Uma boa introdução ao TEI é o livro *What Is the Text Encoding Initiative* de Lou Burnard, [disponível gratuitamente online](https://books.openedition.org/oep/426?lang=en).

- Um bom tutorial para XML está disponível em: [https://www.w3schools.com/xml/](https://www.w3schools.com/xml/) e em: [https://www.tutorialspoint.com/xml/index.htm](https://www.tutorialspoint.com/xml/index.htm).

- O consórcio TEI também oferece [uma boa introdução ao XML](https://www.tei-c.org/release/doc/tei-p5-doc/en/html/SG.html).

- A documentação XML oficial está disponível no [site do consórcio W3C](https://www.w3.org/XML/). [Documentação para toda a família XSL](https://www.w3.org/Style/XSL/) (incluindo XSLT) também está disponível.

- A Fundação Mozilla também fornece uma boa página sobre XSLT e tecnologias relacionadas [(em espanhol)](https://developer.mozilla.org/es/docs/Web/XSLT) e [(em inglês)](https://developer.mozilla.org/en-US/docs/Web/XSLT).

- A página [TTHUB](https://tthub.io) contém uma excelente ["Introducción a la Text Encoding Initiative"](https://tthub.io/aprende/introduccion-a-tei/) de Susanna Allés Torrent (2019).

- Uma lição introdutória de Programming Historian sobre XML e as transformações XSL é [*Transformação de dados com XML e XSL para reutilização*](/en/lessons/data-transformation-xml-xsl), de M. H. Beals.
