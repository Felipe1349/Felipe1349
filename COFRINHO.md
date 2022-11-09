import java.util.Scanner;

public class Principal {
    public static final Scanner ENTRADA = new Scanner(System.in);
    public static final Cofrinho COFRINHO = new Cofrinho();

    public static void main(String... args)
    {
        System.out.println("*** COFRINHO ***");
        menu();
    }

    private static void menu()
    {
        int opcao;

        do {
            System.out.println();
            System.out.println("MENU");
            System.out.println("[1] Adicionar Moeda.");
            System.out.println("[2] Remover Moeda.");
            System.out.println("[3] Listar Todas as Moedas.");
            System.out.println("[4] Calcular Total");
            System.out.println("[5] Sair do Programa");
            System.out.println();
            System.out.print("Escolha uma Opção: ");

            opcao = ENTRADA.nextInt();

            switch (opcao) {
                case 1:
                    MenuAdicionarMoeda.exibir();
                break;

                case 2:
                    MenuRemoverMoeda.exibir();
                break;

                case 3:
                    MenuListaMoedas.exibir();
                break;

                case 4:
                    MenuCalcularTotal.exibir();
                break;

                case 5:
                    break;

                default:
            }
        }
        while(opcao != 5);
    }
}

public class MenuAdicionarMoeda {
    public static void exibir()
    {
        System.out.println("SELEÇÃO DE MOEDAS");
        System.out.println("[1] Dólar ($).");
        System.out.println("[2] Euro (€).");
        System.out.println("[3] Real (R$).");
        System.out.println("[4] Yen (¥).");
        System.out.println("[5] Cancelar adição.");
        System.out.println();
        System.out.print("Escolha uma Opção: ");

        switch(Principal.ENTRADA.nextInt())
        {
            case 1 :
                adicionarDolar();
                break;

            case 2 :
                adicionarEuro();
                break;

            case 3 :
                adicionarReal();
                break;

            case 4 :
                adicionarYen();
                break;

            case 5 :
                break;

            default :
                System.out.println("Opção inválida.");
        }
    }

    private static void adicionarMoeda(int[] valoresPossiveis, String nome, String simbolo)
    {
        String valores = "";
        int valor;

        for(int valorPossivel : valoresPossiveis)
        {
            valores += simbolo + valorPossivel + " ";
        }

        System.out.println("Valores Disponíves Para " + nome);
        System.out.println(valores);
        System.out.println();
        System.out.print("Digite o valor a ser adicionado ou 0(zero) para cancelar: ");

        valor = Principal.ENTRADA.nextInt();

        if(valor == 0)
        {
            return;
        }

        System.out.println();

        try
        {
            switch (nome)
            {
                case Dolar.NOME :
                    Principal.COFRINHO.adicionar(new Dolar(valor));
                break;

                case Euro.NOME :
                    Principal.COFRINHO.adicionar(new Euro(valor));
                break;

                case Real.NOME :
                    Principal.COFRINHO.adicionar(new Real(valor));
                break;

                case Yen.NOME :
                    Principal.COFRINHO.adicionar(new Yen(valor));
            }

            System.out.println("Você colocou " + simbolo + valor + " centavo(s) no cofrinho.");
        }
        catch(MoedaInexistenteException ex)
        {
            System.out.println("Não foi possível adicionar " + simbolo + valor + ", pois, não existe tal moeda.");
        }
    }

    private static void adicionarDolar()
    {
        adicionarMoeda(Dolar.VALORES_POSSIVEIS, Dolar.NOME, Dolar.SIMBOLO);
    }

    private static void adicionarEuro()
    {
        adicionarMoeda(Euro.VALORES_POSSIVEIS, Euro.NOME, Euro.SIMBOLO);
    }

    private static void adicionarReal()
    {
        adicionarMoeda(Real.VALORES_POSSIVEIS, Real.NOME, Real.SIMBOLO);
    }

    private static void adicionarYen()
    {
        adicionarMoeda(Yen.VALORES_POSSIVEIS, Yen.NOME, Yen.SIMBOLO);
    }
}

public class MenuRemoverMoeda {
    public static void exibir()
    {
        int opcao = 0;

        MenuListaMoedas.exibir();

        System.out.println();
        System.out.print("Digite a posição da moeda que deseja retirar do cofrinho, ou digite 0(zero) para cancelar: ");

        opcao = Principal.ENTRADA.nextInt();

        if(opcao == 0)
        {
            return;
        }

        try {
            Principal.COFRINHO.removerNaPosicao(opcao - 1);
        }
        catch(IndexOutOfBoundsException ex)
        {
            System.out.println();
            System.out.println("A posição digitada não existe.");
        }

        MenuListaMoedas.exibir();
    }
}
public class MenuListaMoedas {
    public static void exibir()
    {
        int posicao = 1;

        System.out.println();
        System.out.println("Lista das Moedas Dentro do Cofrinho");
        System.out.println("Total de " + Principal.COFRINHO.moedas().size() + " moeda(s) no cofrinho.");

        for(Moeda moeda : Principal.COFRINHO.moedas())
        {
            System.out.println("[" + posicao++ +"] " + moeda.SIMBOLO + moeda.VALOR);
        }
    }
}

import java.util.ArrayList;

public class Cofrinho {
    private ArrayList<Moeda> moedas = new ArrayList<Moeda>();

    public void adicionar(Moeda moeda)
    {
        moedas.add(moeda);
    }

    public ArrayList<Moeda> moedas()
    {
        return moedas;
    }

    public void removerNaPosicao(int posicao)
    {
        moedas.remove(posicao);
    }
}

import java.util.Arrays;

public abstract class Moeda {
    public final String NOME;
    public final String SIMBOLO;
    public final int VALOR;

    Moeda(String nome, String simbolo, int valor, int[] valoresPossiveis) throws MoedaInexistenteException
    {
        if( ! Arrays.stream(valoresPossiveis).anyMatch(i -> i == valor))
        {
            throw new MoedaInexistenteException();
        }

        NOME = nome;
        SIMBOLO = simbolo;
        VALOR = valor;
    }

    abstract double valorEmReais();
}

public class Dolar extends Moeda {
    public static final String NOME = "Dólar";
    public static final String SIMBOLO = "¢";

    public static final int[] VALORES_POSSIVEIS = { 1, 5, 10, 25, 50 };

    Dolar(int valor) throws MoedaInexistenteException
    {
        super(NOME, SIMBOLO, valor, VALORES_POSSIVEIS);
    }

    public double valorEmReais()
    {
        return VALOR * 0.053;
    }
}

public class Euro extends Moeda {
    public static final String NOME = "Euro";
    public static final String SIMBOLO = "€";
    public static final int[] VALORES_POSSIVEIS = { 1, 2, 5, 10, 20, 50 };

    Euro(int valor) throws MoedaInexistenteException
    {
        super(NOME, SIMBOLO, valor, VALORES_POSSIVEIS);
    }

    public double valorEmReais()
    {
        return VALOR * 0.052;
    }
}
  
public class Real extends Moeda {
    public static final String NOME = "Real";
    public static final String SIMBOLO = "R$";
    public static final int[] VALORES_POSSIVEIS = { 5, 10, 25, 50 };

    Real(int valor) throws MoedaInexistenteException
    {
        super(NOME, SIMBOLO, valor, VALORES_POSSIVEIS);
    }

    public double valorEmReais()
    {
        return VALOR * 0.01;
    }
}

public class Yen extends Moeda {
    public static final String NOME = "Yen";
    public static final String SIMBOLO = "¥";
    public static final int[] VALORES_POSSIVEIS = { 1, 5, 10, 50 };

    Yen(int valor) throws MoedaInexistenteException
    {
        super(NOME, SIMBOLO, valor, VALORES_POSSIVEIS);
    }

    public double valorEmReais()
    {
        return VALOR * 0.00036;
    }
}
  
public class MenuCalcularTotal {
    public static void exibir()
    {
        double total = 0;

        MenuListaMoedas.exibir();

        for(Moeda moeda : Principal.COFRINHO.moedas())
        {
            total += moeda.valorEmReais();
        }

        System.out.println();
        System.out.println("Total no cofre é de R$" + total + " .");
    }
}
  

public class MoedaInexistenteException extends Exception{
}

