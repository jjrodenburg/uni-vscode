# uni-vscode

This is a "universal language" vscode extension based on Antlr
and Language Server Protocol. The code is divided into two parts:
Server and VsCode:

* Server (with Logger, LspHelpers, and Workspaces) is C# code that
implements a LSP server.
* VsCode is Typescript code that implemements the client VSCode
extension.

## How to use

You will need prerequisites:

* .NET SDK
* Trash

Then, follow this script to create an Antlr4 parser for a grammar.

    # Clone the grammars-v4 repo, pick a grammar, and generate a parser for the extension.
    git clone https://github.com/antlr/grammars-v4.git
    cd grammars-v4/java/java
    trgen; cd Generated; make

Then, clone this repo, and set up three files to specify how the server
is to work:

* `~/.grammar-location` is the full path of the grammar, e.g.
`c:\Users\kenne\Documents\GitHub\i2248\java\java`.
* `~/.grammar-classes` is a list, one per line, of the classes
of symbols used. These are chosen from the list of classifications
in Language Server Protocol 3.16.,
[SemanticTokenTypes](https://microsoft.github.io/language-server-protocol/specifications/specification-current/#textDocument_semanticTokens).
The following is an example of six class types to be used.

    class
    property
    variable
    method
    keyword
    string

* `~/.grammar-classifiers` is the list of XPath expressions that
define the terminal symbol searches that denote the classes. E.g.,

    //classDeclaration/IDENTIFIER
    //fieldDeclaration/variableDeclarators/variableDeclarator/variableDeclaratorId/IDENTIFIER
    //variableDeclarator/variableDeclaratorId/IDENTIFIER
    //methodDeclaration/IDENTIFIER
    //(ABSTRACT | ASSERT | BOOLEAN | BREAK | BYTE | CASE | CHAR | CLASS | CONST | CONTINUE | DEFAULT | DO | DOUBLE | ELSE | ENUM | EXTENDS | FINAL | FINALLY | FLOAT | FOR | IF | GOTO | IMPLEMENTS | IMPORT | INSTANCEOF | INT | INTERFACE | LONG | NATIVE | NEW | PACKAGE | PRIVATE | PROTECTED | PUBLIC | SHORT | STATIC | STRICTFP | SUPER | SWITCH | SYNCHRONIZED | THIS | THROW | THROWS | TRANSIENT | TRY | VOID | VOLATILE | WHILE)
    //(DECIMAL_LITERAL | HEX_LITERAL | OCT_LITERAL | BINARY_LITERAL | HEX_FLOAT_LITERAL | BOOL_LITERAL | CHAR_LITERAL | STRING_LITERAL | NULL_LITERAL)

Then, 

    git clone https://github.com/kaby76/uni-vscode.git
    cd uni-vscode
    dotnet build
    cd VsCode
    bash clean.sh; bash install.sh
    code .
    # In VSCode, type F5.

## Implementation

* LSP server in C#.
* VSCode client code in Typescript.
* Grammars are implemented in Antlr4. The parser driver is implemented
using [trgen](https://github.com/kaby76/Domemtech.Trash/tree/main/trgen).
* The LSP server reads two files (.grammar-location and .grammar-classes)
in Environment.GetFolderPath(Environment.SpecialFolder.UserProfile) which
are used to specify the location of the standardized Antlr4 parser and
classes of symbols in XPath notation.
