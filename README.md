# SOLID

## SOLID é um acrônimo que consolida 5 itens que são considerados como boas práticas no mundo do desenvolvimento orientado a objetos.

- **S -- Single Reponsability Principle** (Princípio da Responsabilidade Única);
- **O -- Open-closed Principle** (Princípio Aberto-Fechado)
- **L -- Liskov Substituition Principle** (Princípio da Substituição de Liskov)
- **I -- Interface Segregation Principle** (Princípio da Segregação da Interface)
- **D -- Dependency Inversion Principle** (Princípio da Inversão da Dependência)

## S - Princípio da responsabilidade única

O princípio da responsabilidade única declara que uma classe deve fazer apenas uma coisa e, portanto, deve ter apenas uma razão para ser modificada.

Vamos declarar esse princípio de um modo mais técnico: somente uma alteração em potencial (lógica do banco de dados, lógica de registro e assim por diante) na especificação do software pode ser capaz de alterar a especificação da classe.

Isso significa que, se uma classe for um contêiner de dados, como uma classe Livro ou uma classe Estudante, e se ela tiver campos relativos àquela entidade, ela deve ser alterada apenas quando alterarmos o modelo de dados.

Seguir o princípio da responsabilidade única é importante. Para começar, porque muitas equipes diferentes podem trabalhar no mesmo projeto e editar a mesma classe por motivos diferentes, o que poderia ocasionar incompatibilidade entre os módulos.

Em segundo lugar, isso torna mais fácil o controle de versão. Por exemplo, digamos que temos uma classe persistente que trata das operações do banco de dados, e vemos que ocorreu um commit no GitHub com uma mudança naquele arquivo. Seguindo o princípio, saberemos que essa mudança está relacionada com questões de armazenamento ou relacionadas ao banco de dados.

Conflitos de merge são um outro exemplo. Eles aparecem quando equipes diferentes alteram o mesmo arquivo. Se, no entanto, seguirmos o princípio da responsabilidade única, menos conflitos surgirão – os arquivos terão um único motivo para mudar e os conflitos que existirem serão muito mais fáceis de resolver.

**Armadilhas comuns e antipadrões**
Nesta seção, veremos alguns dos erros comuns que violam o princípio da responsabilidade única. Em seguida, falaremos sobre algumas formas de consertarmos esses erros.

Examinaremos o código de um programa simples de faturamento de uma livraria como exemplo. Vamos começar definindo a classe Livro, que será usada na fatura.

```java

class Livro {
	String nome;
	String nomeAutor;
	int ano;
	int preco;
	String isbn;

	public Livro(String nome, String nomeAutor, int ano, int preco, String isbn) {
		this.nome = nome;
		this.nomeAutor = nomeAutor;
		this.ano = ano;
        this.preco = preco;
		this.isbn = isbn;
	}
}
```

Essa é uma classe Livro simples com alguns campos, nada demais. Não estou criando campos privados para que não precisemos lidar com getters e setters e possamos prestar atenção na lógica.

Agora, vamos criar a classe Fatura, que terá a lógica para o faturamento e o cálculo do preço total. Por agora, vamos considerar que nossa loja vende apenas livros.

```java
public class Fatura {

	private Livro livro;
	private int quantidade;
	private double porcDesconto;
	private double porcImposto;
	private double total;

	public Fatura(Livro livro, int quantidade, double porcDesconto, double porcImposto) {
		this.livro = livro;
		this.quantidade = quantidade;
		this.porcDesconto = porcDesconto;
		this.porcImposto = porcImposto;
		this.total = this.calcularTotal();
	}

	public double calcularTotal() {
	        double preco = ((livro.preco - livro.preco * porcDesconto) * this.quantidade);

		double precoComImposto = preco * (1 + porcImposto);

		return precoComImposto;
	}

	public void imprimirFatura() {
            System.out.println(quantidade + "x " + livro.nome + " " +          livro.preco + "$");
            System.out.println("Porcentagem de desconto: " + porcDesconto);
            System.out.println("Procentagem de imposto: " + porcImposto);
            System.out.println("Total: " + total);
	}

        public void salvarParaArquivo(String nomeArquivo) {
	// Cria um arquivo com o nome especificado e salva a fatura
	}

}
```

Essa é a nossa classe Fatura. Ela também contém alguns campos relativos ao faturamento e 3 métodos:

Método calcularTotal, que calcula o preço total,
Método imprimirFatura, que imprime a fatura no console e
Método salvarParaArquivo, responsável por salvar a fatura em um arquivo.
Dedique uns instantes a entender o que há de errado com o design dessa classe antes de ler o próximo parágrafo.

O que está acontecendo aqui? nossa classe viola o princípio da responsabilidade única de diversas maneiras.

A primeira violação está no método imprimirFatura, que tem nossa lógica de impressão. De acordo com o princípio da responsabilidade única, nossa classe deve ter apenas uma única razão para ser alterada. Essa razão deve ser uma mudança no cálculo da fatura para nossa classes.

Nessa arquitetura, no entanto, se quiséssemos mudar o formato de impressão, precisaríamos mudar a classe. É por isso que não devemos misturar a lógica de impressão com a lógica de negócios na mesma classe.

Temos um outro método que viola o princípio da responsabilidade única em nossa classe: o método salvarParaArquivo. É um erro muito comum misturar a lógica da persistência com a lógica dos negócios.

Não pense em termos de salvar um arquivo – poderíamos estar falando em salvar em um banco de dados, fazer uma chamada à API ou alguma outra coisa relacionada à persistência.

Bem, como podemos, então, consertar esse problema?

Podemos criar novas classes para as lógicas de impressão e persistência, para não precisarmos mais modificar a classe Fatura para esses fins.

Criamos 2 classes, ImpressaoDeFatura e PersistenciaDaFatura, e movemos os métodos.

```java
public class ImpressaoDeFatura {
    private Fatura fatura;

    public ImpressaoDeFatura(Fatura fatura) {
        this.fatura = fatura;
    }

    public void imprimir() {
            System.out.println(fatura.quantidade + "x " + fatura.livro.nome + " " + fatura.livro.preco + "$");
            System.out.println("Porcentagem de desconto: " + fatura.porcDesconto);
            System.out.println("Procentagem de imposto: " + fatura.porcImposto);
            System.out.println("Total: " + fatura.total);
	}
}
```

```java
public class PersistenciaDaFatura {
    Fatura fatura;

    public PersistenciaDaFatura(Fatura fatura) {
        this.fatura = fatura;
    }

    public void salvarParaArquivo(String nomeArquivo) {
		// Cria um arquivo com o nome especificado e salva a fatura
	}
}
```

Agora, nossa estrutura de classe obedece ao princípio de responsabilidade única e cada classe é responsável por um aspecto de nossa aplicação. Ótimo!

## O - Princípio aberto/fechado

O princípio de aberto/fechado diz que as classes devem estar abertas para extensão, mas fechadas para modificação.

Modificação significa alterar o código de uma classe existente, enquanto extensão significa adicionar novas funcionalidades.

O que esse princípio representa, portanto é que: devemos poder adicionar novas funcionalidades sem tocar no código existente para a classe. Isso se dá porque, sempre que modificamos o código existente, estamos nos arriscando a criar bugs em potencial. Assim, devemos evitar de tocar em código em produção testado e confiável (em grande parte), se possível.

No entanto, como podemos adicionar novas funcionalidades sem tocar na classe? Geralmente, isso é feito com o auxílio de interfaces e classes abstratas.

Agora que já tratamos do básico sobre o princípio, vamos aplicar isso ao nosso programa de faturamento.

Digamos que nosso chefe tenha nos dito que deseja que as faturas sejam salvas em um banco de dados onde possamos fazer pesquisas facilmente. Para nós, isso seria fácil, então pedimos pouco tempo!

Criamos o banco de dados, fazemos a conexão a ele e adicionamos um método de salvamento em nossa classe PersistenciaDaFatura:

```java
public class PersistenciaDaFatura {
    Fatura fatura;

    public PersistenciaDaFatura(Fatura fatura) {
        this.fatura = fatura;
    }

    public void salvarParaArquivo(String nomeArquivo) {
		// Cria um arquivo com o nome especificado e salva a fatura
	}

    public void salvarParaBancoDeDados() {
        // Salva a fatura em um banco de dados
    }
}
```

Infelizmente para nós, os desenvolvedores preguiçosos da livraria, não criamos as classes de modo que pudessem ser extensíveis no futuro. Assim, para adicionar esse recurso, modificamos a classe PersistenciaDaFatura.

Se nossa criação das classes tivesse obedecido o princípio de aberto/fechado, não precisaríamos alterar essa classe.

Então, por sermos os desenvolvedores preguiçosos, porém inteligentes, da livraria, percebemos o problema do design e decidimos refatorar o código para que possamos atender àquele princípio.

```java
interface PersistenciaDaFatura {

    public void salvar(Fatura fatura);
}
```

Mudamos o tipo de PersistenciaDaFatura para Interface e adicionamos um método de salvamento. Cada classe de persistência implementará esse método.

```java
public class PersistenciaEmArquivo implements PersistenciaDaFatura {

    @Override
    public void salvar(Fatura fatura) {
        // Salvar em arquivo
    }
}
```

Desse modo, nossa estrutura de classe terá essa aparência:

![Alt](https://www.freecodecamp.org/portuguese/news/content/images/size/w1000/2022/05/SOLID-Tutorial-1-1024x554.jpeg)

Agora, nossa lógica de persistência é facilmente extensível. Se nosso chefe nos pedir outro banco de dados e tiver dois tipos diferentes de BD, como o MySQL e o MongoDB, podemos fazer isso facilmente.

Você pode achar que simplesmente criaríamos diversas classes sem uma interface e adicionaríamos um método de salvamento a cada uma delas.

Digamos, porém, que tenhamos estendido nossa aplicação e que tenhamos diversas classes de persistência, como PersistenciaDaFatura, PersistenciaDeLivro e criamos uma classe GerenteDePersistencia que gere todas as classes de persistência:

```java
public class GerenteDePersistencia {
    PersistenciaDaFatura persistenciaDaFatura;
    PersistenciaDeLivro persistenciaDeLivro;

    public GerenteDePersistencia(PersistenciaDaFatura persistenciaDaFatura,
                               PersistenciaDeLivro persistenciaDeLivro) {
        this.persistenciaDaFatura = persistenciaDaFatura;
        this.persistenciaDeLivro = persistenciaDeLivro;
    }
}
```

Agora, podemos passar qualquer classe que implemente a interface PersistenciaDaFatura para essa classe com o auxílio do polimorfismo. Essa é a flexibilidade fornecida pelas interfaces.

## L - Princípio da substituição de Liskov

O princípio da substituição de Liskov declara que as subclasses devem ser substituíveis por suas classes de base.

Isso quer dizer que, se a classe B for uma subclasse da classe A, devemos poder passar um objeto da classe B para qualquer método que espere um objeto da classe A e o método não deverá produzir resultados estranhos, nesse caso.

Esse é o comportamento esperado, pois, quando usamos a herança, levamos em conta que a classe filha herda tudo o que a superclasse tem. A classe filha estende o comportamento, mas nunca o reduz.

Portanto, quando uma classe não obedece esse princípio, isso causa alguns bugs ruins e difíceis de detectar.

O princípio de Liskov é fácil de entender, mas difícil de detectar no código. Vamos dar uma olhada em um exemplo para entender melhor.

```java
class Retangulo {
	protected int largura, altura;

	public Retangulo() {
	}

	public Retangulo(int largura, int altura) {
		this.largura = largura;
		this.altura = altura;
	}

	public int getLargura() {
		return largura;
	}

	public void setLargura(int largura) {
		this.largura = largura;
	}

	public int getAltura() {
		return altura;
	}

	public void setAltura(int altura) {
		this.altura = altura;
	}

	public int getArea() {
		return largura * altura;
	}
}
```

Temos uma classe Retangulo simples, e uma função getArea que retorna a área do retângulo.

Agora, decidimos criar outra classe para os quadrados. Como você deve saber, um quadrado é apenas um tipo de retângulo onde a largura é igual à altura.

```java
class Quadrado extends Retangulo {
	public Quadrado() {}

	public Quadrado(int tamanho) {
		largura = altura = tamanho;
	}

	@Override
	public void setLargura(int largura) {
		super.setLargura(largura);
		super.setAltura(largura);
	}

	@Override
	public void setAltura(int altura) {
		super.setAltura(altura);
		super.setLargura(altura);
	}
}
```

Nossa classe Quadrado estende a classe Retangulo. Definimos que altura e largura têm o mesmo valor no construtor, mas não queremos que o usuário (aquele que usar nossa classe em seu código) altere altura e largura de maneira a poder violar a propriedade do quadrado.

Assim, sobrescrevemos os setters de ambas as propriedades sempre que uma delas é alterada. Ao fazer isso, no entanto, acabamos de violar o princípio da substituição de Liskov.

Vamos criar uma classe main para realizar testes na função getArea.

```java
class Test {

   static void getAreaTeste(Retangulo r) {
      int largura = r.getLargura();
      r.setAltura(10);
      System.out.println("Área esperada de " + (largura * 10) + ", obteve " + r.getArea());
   }

   public static void main(String[] args) {
      Retangulo rc = new Retangulo(2, 3);
      getAreaTeste(rc);

      Retangulo sq = new Quadrado();
      sq.setLargura(5);
      getAreaTeste(sq);
   }
}
```

O testador da equipe acaba de aparecer com a função de teste getAreaTeste e conta que a função getArea não passa no teste para objetos quadrados.

No primeiro teste, criamos um retângulo onde a largura é 2 e a altura é 3 e chamamos getAreaTeste. O resultado é 20, como esperávamos, mas algo dá errado ao passar o quadrado. Isso ocorre por que chamamos a função setAltura no teste e ela está definindo a largura também. Isso resulta em um retorno inesperado.

## I - Princípio da segregação da interface

Segregação quer dizer manter as coisas separadas. O princípio da segregação da interface tem a ver com separar as interfaces.

O princípio declara que muitas interfaces específicas do cliente são melhores que uma interface de propósito geral. Os clientes não devem ser forçados a implementar uma função que não necessitam.

Esse é um princípio simples de entender e de aplicar. Vamos ver um exemplo.

```java
public interface Estacionamento {

	void estacionarCarro();	// Diminuir contagem de vagas em 1
	void sairDaVagaComCarro(); // Aumentar contagem de vagas em 1
	void getCapacidade();	// Retornar capacidade de carros
	double calcularTaxa(Carro carro); // Retornar o preço com base no número de horas
	void pagar(Carro carro);
}

class Carro {

}
```

Modelamos um estacionamento bem simplificado. É o tipo de estacionamento onde você paga um valor por hora. Imagine, agora, que queremos implementar um estacionamento gratuito.

```java
public class EstacionamentoGratuito implements Estacionamento {

	@Override
	public void estacionarCarro() {

	}

	@Override
	public void sairDaVagaComCarro() {

	}

	@Override
	public void getCapacidade() {

	}

	@Override
	public double calcularTaxa(Carro carro) {
		return 0;
	}

	@Override
	public void pagar(Carro carro) {
		throw new Exception("Estacionamento gratuito");
	}
}
```

Nossa interface de estacionamento era composta de 2 coisas: lógica relacionada ao estacionamento (estacionar, sair da vaga com o carro, obter a capacidade) e lógica relacionada ao pagamento.

O problema é ser muito específica. Por causa disso, nossa classe EstacionamentoGratuito foi forçada a implementar métodos relacionados ao pagamento que são irrelevantes. Vamos separar o segregar as interfaces.

![Alt](https://www.freecodecamp.org/portuguese/news/content/images/size/w1000/2022/05/SOLID-Tutorial-1024x432.png)

Você, agora, separou os estacionamentos. Com esse novo modelo, podemos até ir mais longe e dividir EstacionamentoPago para dar suporte a tipos diferentes de pagamento.

Agora, nosso modelo é muito mais flexível, extensível e os clientes não precisam implementar lógica irrelevante, pois fornecemos somente funcionalidade relacionada ao estacionamento na interface de estacionamento.

## D - Princípio da inversão da dependência

O princípio da inversão da dependência declara que nossas classes devem depender de interfaces ou de classes abstratas em vez de classes concretas e de funções.

[Neste artigo](https://fi.ort.edu.uy/innovaportal/file/2032/1/design_principles.pdf)(2000, texto em inglês), Uncle Bob resume esse princípio da seguinte maneira:

> "Se o princípio de aberto/fechado declara o objetivo da arquitetura orientada a objetos, o princípio de inversão da dependência declara seu mecanismo principal".

Esses dois princípios, de fato, estão relacionados. Aplicamos esse padrão anteriormente enquanto discutíamos o princípio de aberto/fechado.

Queremos que nossas classes estejam abertas para extensão, por isso reorganizamos nossas dependências para que dependam de interfaces em vez de classes concretas. Nossa classe GerenteDePersistencia depende de PersistenciaDaFatura em vez de classes que implementam aquela interface.

### Fonte: (https://www.freecodecamp.org/portuguese/news/os-principios-solid-da-programacao-orientada-a-objetos-explicados-em-bom-portugues/)
