# Refatoração
- Refatoração
    - Conceito
    - Teste
    - Referências
- **Conceito:** O processo de aplicar mudanças para melhorar código já escrito anteriormente sem alterar o comportamento externo da aplicação é chamado de Refatoração.
- **Teste:** A cada mudança que aplicamos em nosso projeto para alcançar maior legibilidade precisamos testar se tudo continua funcionando como estava.
- **Referência bibliográfica:** _Refactoring: Improving the Design of Existing Code_; _The Pragmatic Programmer_.
- [Architect Modern Web Applications with ASP.NET Core and Azure](https://learn.microsoft.com/en-us/dotnet/architecture/modern-web-apps-azure/)

# Pilares da Programação Orientada a Objeto
- Encapsulamento
## Encapsulamento
O pilar do encapsulamento (_encapsulation_) lida com a segregação da informação e sua manipulação dentro de uma única classe. Sua finalidade é criar códigos mais fáceis de entender, concentrar a manutenção de uma informação, e manter a integridade da informação. O encapsulamento é feito através da limitação do acesso a propriedades e métodos, bem como na limitação do retorno dos métodos.
### Coleções .NET
O (_Diretrizes para Coleções_)[https://learn.microsoft.com/pt-br/dotnet/standard/design-guidelines/guidelines-for-collections] trata de boas práticas no uso de coleções de objetos que tornam o código mais seguro, especialmente coleções que implementam o tipo `IEnumerable<T>`, sendo algumas as seguintes:
- Não usar o tipo `List<T>` em APIs públicas, pois o seu retorno é alterável pelo cliente da solicitação. Optar por seu uso em implementações internas.
- Para coleções somente leitura, usar o tipo `ReadOnlyColletion<T>`.
- Para acessar propriedades de coleções de leitura/gravação, optar pelo tipo `Collection<T>` ou classes da sua hierarquia.
- Preferir `IEnumerable<T>` a `ICollection<T>` para acessar propriedades como `Count`.

# Legibilidade
## Usar código para explicar o código
- **Comentários:** Depender de comentários para explicar um código é ruim, porque o comentário dificilmente será atualizado quando o código for alterado. Nesse caso, convém limpar os comentários substituindo-os por variáveis com nomes signifiativos.
- **Nomes significativos:** O ideal é dar nomes significativos para as variáveis, isto é, nomes que efetivamente representam o seu valor (v. documentação da Microsoft). O mesmo vale para arquivos e métodos.
- **Nomes grandes:** O nome significativo pode ficar grande, o que não é um problema.
- **Nomes contextualizados:** O nome da variável deve depender do contexto. Portanto, um mesmo valor pode ser atribuído a uma variável diferente, cujo nome corresponde ao contexto diferente.
- **Sufixo 'Async':** O ideal é usar o sufixo Async ao final dos métodos assíncronos, porque fica mais fácil de identificar métodos assíncronos.
- **Renomeação:** O primeiro nome que damos a um arquivo, método, variável ou classe talvez não seja tão significativo quanto o ideal. Por isso, conforme o código vai sendo alterado com o tempo, os nomes podem ser revistos, aproveitando das ferramentas de refências da IDE.
- [Artigo '_General Naming Conventions_' da Microsoft.](https://learn.microsoft.com/en-us/dotnet/standard/design-guidelines/general-naming-conventions)
- **Muitas linhas de código:** Um arquivo com muitas linhas de código é ruim, pois exige _scrolling_ para ler e entender o que acontece no código.
- **Exração de classes:** Na POO, o ideal é isolar instruções em classes específicas.
- **Parâmetro nomeado:** Nomear o parâmetro ao chamar um método melhora a legibilidade do código.

# Código Ambíguo
- Problemas do código repetido
- DRY
- Attribute .NET
## DRY
O código repetido - ou duplicado, ou ambíguo - pode complicar a manutenção, porque uma informação repetida cria diferentes pontos de manutenção iguais, já que a atualização da informação exige a atualização de cada ponto diferente. O ideal é ter uma única fonte para a informação, reutilizável, para concentrar a manutenção em um único lugar. Isso significa aplicar o _Don't Repeate Yourself_. Disse Alexandre Aquiles (_Desbravando o Solid_, 2022, p. 38 ): “Todo bloco de conhecimento deve ter uma representação única, sem ambiguidades e dominante num sistema”.
## Extração de classe
A instrução repetida pode ser centralizada em um único arquivo em uma classe própria. Por exemplo, a lógica responsável pela leitura de arquivos pode estar em uma classe apropriada para essa finalidade, sendo invocada pelas classes que precisarem dela.
## Extração de função
A instrução repetida pode estar em um método próprio dentro de uma mesma classe.
## Atributos .NET
Usar uma classe atributo é uma forma de evitar a repetição de código.
Trata-se de uma classe cujos dados são armazenados na definição de metadados do assembly, e acessados em tempo de execução via as APIs de Reflexão (_reflection_).
A classe atributo herda de `System.Attribute`, e recebe um atributo para atributos personalizados - no caso, `AttributeTargets.Class`. Veja-se o código abaixo:
```csharp
[AttributeUsage(AttributeTargets.Class)] public class AmostraAttribute : Attribute { public string Texto { get; } }
```
Esse atributo fica disponível para ser usados por outras classes.
```csharp
[MinhaClasse("Hello, World.")] // Uso do atributo.
public class MinhaClasse {
    private readonly Dictionary<string, AmostraAttribute> Atributos;
    public MinhaClasse()
    {
        Docs = Assembly.GetExecutingAssembly() // Acesso em tempo de execução no assembly com Reflexão.
                       .GetTypes() // Acesso aos tipos de metadados do assembly.
                       .Where(t => t.GetCustomAttributes<AmostraAttribute>().Any()) // Obtém apenas atributos do tipo AmostraAttribute
                       .Select(t => t.GetCustomAttribute<AmostraAttribute>()!) // Obtém lista dos atributos
                       .ToDictionary(d => d.Texto); // Transforma lista em dicionário
    }
}
```
Veja-se o artigo [Attributes](https://learn.microsoft.com/en-us/dotnet/standard/design-guidelines/attributes) para ver boas práticas no uso de atributos.
