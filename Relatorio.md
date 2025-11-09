# Relatório Técnico - Sistema de Gerenciamento de Biblioteca

## 1. Descrição dos Módulos e Sub-rotinas

### **Módulo Principal (`menu`)**
- **Função**: Controla o fluxo principal do programa
- **Sub-rotinas**: 
  - Chama todas as outras funções baseadas na escolha do usuário
  - Gerencia o loop infinito do programa
  - Implementa tratamento de sinais (trap)

### **Módulo de Cabeçalho (`cabecalho`)**
- **Função**: Exibe informações institucionais e data/hora atual
- **Características**: Mostra dados da equipe, instituição e timestamp

### **Módulo de Gestão de Livros (`adicionar_remover_arquivos`)**
- **Sub-módulos**:
  - `adicionar`: Cadastra novos livros com validação de campos
  - `remover`: Remove livros por ID com verificação de existência

### **Módulo de Consulta (`listar`, `buscar_livro`)**
- **Funções**:
  - `listar`: Exibe livros com opções de visualização
  - `buscar_livro`: Busca por título ou autor (case insensitive)

### **Módulo de Edição (`editar_arquivo`, `alterar`)**
- **Função**: Permite modificar dados dos livros
- **Características**: Sistema de confirmação em duas etapas

### **Módulo de Utilitários (`popular`)**
- **Função**: Popula a base com dados de exemplo para testes

---

## 2. Listagem de Comandos e Propósitos

### **Comandos de Entrada/Saída**
- `echo`: Exibe mensagens e resultados para o usuário
- `read`: Captura entrada do usuário para menus e dados
- `cat`: Exibe conteúdo completo de arquivos
- `more`: Exibe conteúdo paginado para melhor visualização

### **Comandos de Processamento de Texto**
- `grep`: Busca padrões em arquivos (IDs, livros, autores)
- `sed`: Edita arquivos in-place (remoção de registros)
- `cut`: Extrai campos específicos de linhas formatadas
- `sort`: Ordena dados numéricos para determinar próximo ID
- `tail`: Obtém o último elemento de listas ordenadas
- `wc -l`: Conta linhas para verificar existência de dados

### **Comandos de Controle de Arquivos**
- `touch`: Cria arquivos necessários para o sistema
- `mv`: Move/renomeia arquivos temporários
- `>`: Redirecionamento para limpar/conteúdo de arquivos
- `>>`: Redirecionamento para adicionar conteúdo

### **Comandos de Controle de Fluxo**
- `case`: Estrutura principal de menus e validações
- `while`: Loops para menus secundários
- `if`: Condicionais para validações e verificações
- `trap`: Manipulação de sinais do sistema

### **Comandos de Utilitários**
- `clear`: Limpa a tela para melhor interface
- `sleep`: Pausa a execução para leitura de mensagens
- `date`: Obtém data e hora para timestamp

---

## 3. Referências Utilizadas

### **Guias e Documentação**
- **Foca Linux**: Guia de referência para comandos bash e shell scripting
- **DeepSeek**: Assistente para desenvolvimento e troubleshooting de código
- **jdoodle**: Editor de Bash Shell online
### **Conceitos Aplicados**
- Estruturas de controle (loops, condicionais)
- Manipulação de arquivos de texto
- Processamento de strings e padrões
- Design de interfaces de linha de comando
- Validação de entrada de usuário

---

## 4. Script Completo

```bash
#!/bin/bash

trap '' 2 

touch biblioteca_log.txt
touch erro.txt

cabecalho () {
    
    echo "###############################################################
# IBMEC                                                       #
# Sistemas Operacionais Semestre 2 de 2025                    #
# Código: IBM0791 Turma: 8001                                 #
# Professor: Luiz Fernando T. de Farias                       #
#-------------------------------------------------------------#
# Equipe Desenvolvedora:                                      #
# Aluno: Lucas Alves Castela Pereira                          #
# Aluno: Rodrigo de Souza Ferrett                             #
# Aluno: Sarah Ferrari  Rodrigues                             #
#-------------------------------------------------------------#
# Rio de Janeiro, `date +%d` de `date +%b` de `date +%Y`                           #
# Hora do Sistema: `date +%H` Horas e `date +%M` Minutos                      #
###############################################################"
}

menu () {
while true
    do
    clear
    cabecalho
echo "Menu de escolhas: 

    1) Adicionar ou remover livros
    2) Listar livros
    3) Buscar livro
    4) Editar dados
    5) Finalizar programa"
    
    
    read escolha
    case $escolha in

        1)
	clear
            adicionar_remover_arquivos
            ;;

        2)
            clear 
	listar
	;;

        3)
clear
            buscar_livro
            ;;

        4)
	clear
            editar_arquivo
            ;;
    
        5)
            trap 2
            break
            ;;    
        popular)
        popular
	;;
        *)
            echo "Opção inválida"
            sleep 1
            ;;
         

esac
    done
}

adicionar_remover_arquivos () {
    cabecalho
    
    while true
    do
echo "Menu de escolhas: 

    1) Adicionar
    2) Remover
    3) Voltar"
    
    
    read escolha
    case $escolha in

        1)
            sleep 1
	clear
            adicionar
            ;;

        2)
            sleep 1
	clear
            remover
            ;;

        3)
            break
            ;;

        *)
            echo "Opção inválida" 
            sleep 1
            ;;

esac
    done
    
    
}

adicionar () {
    #formulário para inserção do livro
    echo "Digite o nome do livro."
    read livro
livro_semespaco=`echo $livro`
case "$livro_semespaco" in
    	"")
echo "Erro:Insira algo neste campo!"
sleep 1
        clear
        return
;;
esac
    echo "Digite o nome do autor do livro."
    read autor
autor_semespaco=`echo $autor`
case "$autor_semespaco" in
    	"")
echo "Erro:Insira algo neste campo!"
sleep 1
        clear
        return
;;
esac

    echo "Digite uma descrição breve do livro"
    read desc
desc_semespaco=`echo $desc`
case "$desc_semespaco" in
    	"")
echo "Erro:Insira algo neste campo!"
sleep 1
        clear
        return
;;
esac

    echo "Digite a quantidade em estoque do livro"
    read qtd
qtd_semespaco=`echo $qtd`
#verifica se a quantidade é um número
case "$qtd_semespaco" in
    *[!0-9]*) 
        echo "Erro: quantidade deve ser um número!"
        sleep 1
        clear
        return
        ;;
	"")
echo "Erro: quantidade deve ser um número!"
sleep 1
        clear
        return
;;
esac


    #Código que determina o ID
    #Verifica se tem algo no arquivo
    if [ -s biblioteca_log.txt ]; then
    #pega apenas os ids, depois pega apenas os números, ordena eles em ordem alfabetica e pega o maior (último)
        maior_id=$(grep -o '\[ID\]: [0-9]*' biblioteca_log.txt | cut -d' ' -f2 | sort -n | tail -1)
        id=$((maior_id + 1))
    else
        id=0
    fi


    echo "[ID]: $id | [LIVRO]: $livro_semespaco | [AUTOR]: $autor_semespaco | [DESC]: $desc_semespaco | [QTD]: $qtd_semespaco" >> biblioteca_log.txt

echo "
Livro adicionado com sucesso
"
}

remover() {
    echo "Digite o ID do livro a ser removido"
    read id
    # Verifica se o ID existe
    if grep -q "\[ID\]: $id |" biblioteca_log.txt; then
        # Pega o livro que será removido para mostrar
        livro_remover=$(grep "\[ID\]: $id |" biblioteca_log.txt)
        
        # Remove a linha
        sed -i "/\[ID\]: $id |/d" biblioteca_log.txt
        echo "Livro removido com sucesso: $livro_remover"
    else
        echo "Erro: ID $id não existe"
    fi

}

listar() {
    while true
    do
        cabecalho
        echo "Menu de Listagem:

    1) Listar todos os livro
    2) Listar por estoque
    3) Voltar ao menu principal" 

        read escolha_busca
        case $escolha_busca in

            1)
            clear
            cabecalho
            
            echo "--- Lista de Livros Cadastrados ---"
            echo ""
            
            cat biblioteca_log.txt | more
            
            echo "" 
            echo "Pressione [Enter] para voltar ao menu..."
            read
	clear
            ;;

            2)

	echo "Digite a quantidade máxima de estoque"
	read limite
	limite_semespaco=`echo $limite`
	case "$limite_semespaco" in
        *[!0-9]*) echo "Número inválido!"
 sleep 1
 ;;
"")
echo "Erro: quantidade deve ser um número!"
sleep 1
;;
        *) 
            echo ""
            echo "Resultados (estoque <= $limite):"
            grep "\[QTD\]: [0-9]*" biblioteca_log.txt | while read linha; do
                qtd=$(echo "$linha" | grep -o '\[QTD\]: [0-9]*' | cut -d' ' -f2)
                [ -n "$qtd" ] && [ "$qtd" -le "$limite" ] && echo "$linha"
            done
            ;;
    esac
	echo "Pressione [Enter] para continuar"
	read
clear

                                ;;
            3) 
                break
                ;;

            *) 
                echo "Opção inválida"
                sleep 1
                ;;

        esac
    done
}


editar_arquivo () {
    cabecalho
    
    while true
    do
echo "Menu de escolhas: 

    1) Alterar nome do livro
    2) Alterar nome do autor
    3) Alterar descricao
    4) Alterar quantidade
    5) Voltar"
    
    
    read escolha
    case $escolha in

        1)
	        clear
            alterar "LIVRO" "Nome do Livro"
            ;;

        2)
            clear
            alterar "AUTOR" "Nome do Autor"
            ;;

        3)
            clear
            alterar "DESC" "Descrição"
            ;;
       4)
            clear
            alterar "QTD" "Quantidade"
            ;;
       5)
            clear
            break
            ;;

        *)
            echo "Opção inválida"
            sleep 1
            ;;

esac
    done
    
    
}



buscar_livro() {
    while true
    do
        cabecalho
        echo "Menu de Busca:

    1) Buscar por nome do livro
    2) Buscar por nome do autor
    3) Voltar ao menu principal" 

        read escolha_busca
        case $escolha_busca in

            1) 
                echo "Digite o nome do livro que deseja buscar:"
                read termo_busca
                clear
                cabecalho
                echo "--- Resultados da busca por livro: '$termo_busca' ---"
                echo ""
                grep -i "$termo_busca" biblioteca_log.txt
                # Busca o termo na biblioteca_log
                # -i = case insensitive (ignora letras maiúsculas ou minúsculas)
                echo ""
                echo "Pressione [Enter] para continuar…"
                read
                clear
                ;;

            2)
                echo "Digite o nome do autor que deseja buscar:"
                read termo_busca
                clear
                cabecalho
                echo "--- Resultados da busca por autor: '$termo_busca' ---"
                echo ""
                grep -i "$termo_busca" biblioteca_log.txt
                echo ""
                echo "Pressione [Enter] para continuar…"
                read
                clear
                ;;
            3) 
                break
                ;;

            *) 
                echo "Opção inválida"
                sleep 1
                ;;

        esac
    done
}

alterar() {
    #Pega os dois parâmetros passados após a chamada de função
    campo="$1"
    nome="$2"

    clear 
    cabecalho
            
    echo "--- Alterar $nome ---"
    echo " "
    echo "Digite o id do livro para pesquisa:"
    read id_busca
    
    #Faz uma pesquisa na biblioteca_log.txt para verificar se o id existe
    linha=`grep "\[ID\]: $id_busca |" biblioteca_log.txt`
    #Verifica se a variavel linha é vazia ou nao (caso for retorna)
    if [ -z "$linha" ]; then
        echo "ID não encontrado!"
        return
    fi
    
    clear
    cabecalho
    
    echo "--- Livro para alterar ---"
    echo $linha
    
    
    while true; do
        echo ""
        echo "Esse é o livro certo? (Y/N):"
        read resposta
        #Pega a resposta sem espaços
        resposta=`echo $resposta`
        case $resposta in
            y|Y)
                break
                ;;
            n|N)
                echo "Retornando"
                return
                ;;
            *)
                echo "Opção inválida."
                sleep 1
                ;;
        esac
    done
    

    while true; do
        echo "Digite o(a) novo(a) $nome:"
        read alteracao
        #pega a alteracao sem espacos
        alteracao_sem_espacos=`echo $alteracao`

        #Verifica se ela é vazia ou nao (se for manda o input novamente, caso for válida continua)
        if [ -z "$alteracao_sem_espacos" ]; then
            echo "Erro: $nome inválido"
            sleep 1
        else
            break
        fi
    done
    
    #Pega cada informacão do livro (cut -d'|' separa em colunas apartir do | na formatacao e apos isso ele faz cut -d':' que faz duas colunas (a da direita(-f2) sendo a informacão do livro
    id=`echo $linha | cut -d'|' -f1 | cut -d':' -f2`
    livro=`echo $linha | cut -d'|' -f2 | cut -d':' -f2`
    autor=`echo $linha | cut -d'|' -f3 | cut -d':' -f2`
    desc=`echo $linha | cut -d'|' -f4 | cut -d':' -f2`
    qtd=`echo $linha | cut -d'|' -f5 | cut -d':' -f2`
    #Tira os espaços das variáveis
    id=`echo $id`
    livro=`echo $livro`
    autor=`echo $autor`
    desc=`echo $desc`
    qtd=`echo $qtd`

     #Monta o novo livro apartir de oque o usuário selecionou
    case $campo in
        "QTD")
	#Verifica se a qtd é um número ou nao
            case $alteracao in 
                *[!0-9]*) 
                    echo "Erro: quantidade deve ser um número!"
                    sleep 1
                    clear
                    return
                    ;;
                *)
                    linha_nova="[ID]: $id | [LIVRO]: $livro | [AUTOR]: $autor | [DESC]: $desc | [QTD]: $alteracao"
                    ;;
                esac
            ;;
        "LIVRO")
            linha_nova="[ID]: $id | [LIVRO]: $alteracao | [AUTOR]: $autor | [DESC]: $desc | [QTD]: $qtd"
            ;;
        "AUTOR")
            linha_nova="[ID]: $id | [LIVRO]: $livro | [AUTOR]: $alteracao | [DESC]: $desc | [QTD]: $qtd"
            ;;
        "DESC")
            linha_nova="[ID]: $id | [LIVRO]: $livro | [AUTOR]: $autor | [DESC]: $alteracao | [QTD]: $qtd"
            ;;
    esac
    
    clear
    cabecalho
    
    while true 
    do
        echo ""
        echo "--- Dados Antigos ---"
        echo $linha
        echo ""
        echo "--- Dados Alterados ---"
        echo $linha_nova
        echo ""
        echo "Confirma a mudança? (Y/N)"
        read confirmacao
        #Pega a confirmação sem espaços
        confirmacao=`echo $confirmacao`
        case $confirmacao in
            y|Y)
                #Ele pega tudo que nao tenha o id buscado e coloca em uma lista temporaria (uma lista sem o item para alterar)
                grep -v "\[ID\]: $id_busca |" biblioteca_log.txt > temp.txt
                #Adicionao livro alterado nessa lista nova
                echo "$linha_nova" >> temp.txt
                #Muda o conteudo da biblioteca_log.txt para da lista nova e deleta a lista temporaria
                mv temp.txt biblioteca_log.txt
                
                echo ""
                echo "Alterando..."
                sleep 2
                echo ""
                echo "Alteração realizada com sucesso!"
                sleep 2
                break
                ;;
            n|N)
                echo ""
                echo "Alteração cancelada"
                return
                ;;
            *)
                echo "Opção inválida."
                sleep 1
                ;;
        esac
    done
}






popular() {
    echo "Populando a biblioteca com 15 livros de exemplo..."
    
    # Limpa o arquivo primeiro
    > biblioteca_log.txt
    
    # Adiciona 15 livros de exemplo
    echo "[ID]: 0 | [LIVRO]: O Senhor dos Anéis | [AUTOR]: J.R.R. Tolkien | [DESC]: Fantasia épica | [QTD]: 5" >> biblioteca_log.txt
    echo "[ID]: 1 | [LIVRO]: 1984 | [AUTOR]: George Orwell | [DESC]: Ficção distópica | [QTD]: 3" >> biblioteca_log.txt
    echo "[ID]: 2 | [LIVRO]: Dom Casmurro | [AUTOR]: Machado de Assis | [DESC]: Romance brasileiro | [QTD]: 7" >> biblioteca_log.txt
    echo "[ID]: 3 | [LIVRO]: Harry Potter e a Pedra Filosofal | [AUTOR]: J.K. Rowling | [DESC]: Fantasia juvenil | [QTD]: 8" >> biblioteca_log.txt
    echo "[ID]: 4 | [LIVRO]: O Pequeno Príncipe | [AUTOR]: Antoine de Saint-Exupéry | [DESC]: Literatura infantil | [QTD]: 6" >> biblioteca_log.txt
    echo "[ID]: 5 | [LIVRO]: Crime e Castigo | [AUTOR]: Fiódor Dostoiévski | [DESC]: Romance psicológico | [QTD]: 4" >> biblioteca_log.txt
    echo "[ID]: 6 | [LIVRO]: Orgulho e Preconceito | [AUTOR]: Jane Austen | [DESC]: Romance clássico | [QTD]: 5" >> biblioteca_log.txt
    echo "[ID]: 7 | [LIVRO]: A Metamorfose | [AUTOR]: Franz Kafka | [DESC]: Ficção absurda | [QTD]: 3" >> biblioteca_log.txt
    echo "[ID]: 8 | [LIVRO]: Cem Anos de Solidão | [AUTOR]: Gabriel García Márquez | [DESC]: Realismo mágico | [QTD]: 6" >> biblioteca_log.txt
    echo "[ID]: 9 | [LIVRO]: O Cortiço | [AUTOR]: Aluísio Azevedo | [DESC]: Romance naturalista | [QTD]: 4" >> biblioteca_log.txt
    echo "[ID]: 10 | [LIVRO]: A Moreninha | [AUTOR]: Joaquim Manuel de Macedo | [DESC]: Romance romântico | [QTD]: 5" >> biblioteca_log.txt
    echo "[ID]: 11 | [LIVRO]: O Hobbit | [AUTOR]: J.R.R. Tolkien | [DESC]: Aventura fantástica | [QTD]: 7" >> biblioteca_log.txt
    echo "[ID]: 12 | [LIVRO]: A Revolução dos Bichos | [AUTOR]: George Orwell | [DESC]: Sátira política | [QTD]: 6" >> biblioteca_log.txt
    echo "[ID]: 13 | [LIVRO]: Memórias Póstumas de Brás Cubas | [AUTOR]: Machado de Assis | [DESC]: Romance realista | [QTD]: 4" >> biblioteca_log.txt
    echo "[ID]: 14 | [LIVRO]: O Guarani | [AUTOR]: José de Alencar | [DESC]: Romance indianista | [QTD]: 5" >> biblioteca_log.txt
    
    echo "Biblioteca populada com 15 livros de exemplo!"
    echo "Pressione [Enter] para continuar..."
    read
}

menu
```

---

**Sistema Desenvolvido por**: Lucas Alves Castela Pereira, Rodrigo de Souza Ferrett, Sarah Ferrari Rodrigues  
**Disciplina**: Sistemas Operacionais - IBMEC Rio de Janeiro  
**Data**: 2025
