# CadastroIPA
Uma tela simples de cadastro para uma ONG
import PySimpleGUI as sg
import psycopg2

def limpar():
    window['-Id_aluno-'].update('')
    window['-Nome_alu-'].update('')
    window['-Email_alu-'].update('')
    window['-Num_cel-'].update('')
    window['-CPF_alu-'].update('')
    window['-SEXO-'].update('')
    window['-Profissao-'].update('')
    window['-DATA_NASC-'].update('')
    window['-CEP_alu-'].update('')
    window['-Cidade-'].update('')
    window['-UF-'].update('')
    window['-Nome_rua-'].update('')
    window['-Num_end-'].update('')
    window['-Bairro -'].update('')


def atualiza():
    if len(lista) == 0:
        limpar()
    else:
        window['-Id_aluno-'].update( lista[indice][0] )
        window['-Nome_alu-'].update( lista[indice][1] )
        window['-Email_alu-'].update( lista[indice][2] )
        window['-Num_cel-'].update( lista[indice][3] )
        window['-CPF_alu-'].update( lista[indice][4] )
        window['-Sexo_alu-'].update( lista[indice][5] )
        window['-Profissao-'].update( lista[indice][6] )
        window['-DATA_NASC-'].update( lista[indice][7] )
        window['-CEP_alu-'].update( lista[indice][8] )
        window['-Cidade-'].update( lista[indice][9] )
        window['-UF-'].update( lista[indice][10] )
        window['-Nome_rua-'].update( lista[indice][11] )
        window['-Num_end-'].update( lista[indice][12] )
        window['-Bairro -'].update( lista[indice][13] )
        




def todos():
    global indice
    global lista
    resposta = []
    with con:
        with con.cursor() as cursor:
            cursor.execute("SELECT * FROM aluno;")
            resposta = cursor.fetchall()
    lista.clear()
    for i in range(len(resposta)):
        lista.append( list(resposta[i]) )

    sg.popup('Quantidade de registros: ' + str(len(resposta)))
    indice = 0
    atualiza()

# Inicialização BD
con = psycopg2.connect(host="localhost", database="IPA.Projeto", user="postgres", password="123456")
with con:
    with con.cursor() as cursor:
        cursor.execute("""CREATE TABLE IF NOT EXISTS Aluno (
            Id_aluno SERIAL PRIMARY KEY UNIQUE NOT NULL,
            Nome_alu VARCHAR(75) not null,
	        Sexo_alu CHAR(1) not null CHECK (Sexo_alu IN ('M', 'F')),
	        CPF_alu CHAR(16) unique not null,
	        Nome_rua VARCHAR(100) not null,
	        Num_end SMALLINT not null,
	        Email_alu VARCHAR(50) unique not null,
	        CEP_alu CHAR(11) not null,
	        UF CHAR(4) not null,
	        Num_cel BIGINT not null,
	        Data_nasc DATE not null,
	        Profissao VARCHAR(30),
	        Bairro VARCHAR(50),
	        Cidade VARCHAR(15)
            );""")

lista=[]
indice = 0

layout = [
    [sg.Image('IPAB.png', size=(1000,100))],
    [
        sg.Text("ID:", size=(8, 1)),
        sg.InputText(size=(6, 1), key="-Id_aluno-", focus=False)
    ],
    [
        sg.Text("Nome:", size=(8, 1)),
        sg.InputText(size=(40, 1), key="-Nome_alu-", focus=True)
    ],
    [
        sg.Text("E-mail:", size=(8, 1)),
        sg.InputText(size=(40, 1), key="-Email_alu-")
    ],
    [
        sg.Text("Celular:", size=(8, 1)),
        sg.InputText(size=(40, 1), key="-Num_cel-")
    ],
    [
        sg.Text("CPF:", size=(8, 1)),
        sg.InputText(size=(40, 1), key="-CPF_alu-")
    ],
    [
        sg.Text("Sexo:", size=(8, 1)),
        sg.InputText(size=(40, 1), key="-Sexo_alu-")
    ],
    [
        sg.Text("Profissão:", size=(8, 1)),
        sg.InputText(size=(40, 1), key="-Profissao-")
    ],
    [
        sg.Text("Data. Nasc:", size=(8, 1)),
        sg.InputText(size=(40, 1), key="-Data_nasc-")
    ],
    [
        sg.Text("CEP:", size=(8, 1)),
        sg.InputText(size=(40, 1), key="-CEP_alu-")
    ],
    [
        sg.Text("Cidade:", size=(8, 1)),
        sg.InputText(size=(40, 1), key="-Cidade-")
    ],
    [
        sg.Text("UF:", size=(8, 1)),
        sg.InputText(size=(40, 1), key="-UF-")
    ],
    [
        sg.Text("Rua:", size=(8, 1)),
        sg.InputText(size=(40, 1), key="-Nome_rua-")
    ],
    [
        sg.Text("Num. Endereço:", size=(8, 1)),
        sg.InputText(size=(40, 1), key="-Num_end-")
    ],
    [
        sg.Text("Bairro:", size=(8, 1)),
        sg.InputText(size=(40, 1), key="-Bairro-")
    ],
    [
        sg.Button('Limpar', size=(8, 1), key="-LIMPAR-"),
        sg.Button('Inserir', size=(8, 1), key="-INSERIR-"),
        sg.Button('Atualizar', size=(8, 1), key="-ATUALIZAR-"),
        sg.Button('Remover', size=(8, 1), key="-REMOVER-")
    ],
    [
        sg.Button('<<', size=(8, 1), key="-<<-"),
        sg.Button('Procurar', size=(8, 1), key="-PROCURAR-"),
        sg.Button('Todos', size=(8, 1), key="-TODOS-"),
        sg.Button('>>', size=(8, 1), key="->>-")
    ]
]

window = sg.Window("Aluno", layout, use_default_focus=False)

# Run the Event Loop
while True:
    event, values = window.read()

    if event == sg.WIN_CLOSED:
        break
    elif event == "-LIMPAR-":
        limpar()
    elif event == "-INSERIR-":
        with con:
            with con.cursor() as cursor:
                cursor.execute("INSERT INTO Aluno (Nome_alu, Email_alu, Num_cel, CPF_alu, Sexo_alu, Profissao, Data_nasc, CEP_alu, Cidade, UF, Nome_rua, Num_end, Bairro ) VALUES (%s, %s, %s, %s,  %s,  %s,  %s,  %s,  %s,  %s,  %s,  %s,  %s);",
                    (values['-Nome_alu-'], values['-Email_alu-'], values['-Num_cel-'], values['-CPF_alu-'], values['-Sexo_alu-'], values['-Profissao-'], values['-Data_nasc-'], values['-CEP_alu-'], values['-Cidade-'], values['-UF-'], values['-Nome_rua-'], values['-Num_end-'], values['-Bairro-']))
        limpar()
    elif event == "-ATUALIZAR-":
        with con:
            with con.cursor() as cursor:
                cursor.execute("UPDATE Alunos SET Nome_alu = %s, Email_alu = %s, Num_cel = %s, CPF_alu = %s, Sexo_alu = %s, Profissao = %s, Data_nasc = %s, CEP_alu = %s, Cidade = %s, uf = %s, rua = %s, Num_end = %s, Bairro = %s WHERE id = %s",
                (values['-Nome_alu-'], values['-Email_alu-'], values['-Num_cel-'], values['-CPF_alu-'], values['-Sexo_alu-'], values['-Profissao-'], values['-Data_nasc-'], values['-CEP_alu-'], values['-Cidade-'], values['-UF-'], values['-RUA-'], values['-Num_end-'], values['-Bairro-'], values['-ID-']))        
    elif event == "-REMOVER-":
        with con:
            with con.cursor() as cursor:
                cursor.execute("DELETE FROM Aluno WHERE id = %s", (values['-Id_aluno-'],))
        lista.pop(indice)
        indice -= 1
        atualiza()
    elif event == "-PROCURAR-":
        with con:
            with con.cursor() as cursor:
                cursor.execute("SELECT * FROM Aluno WHERE nome LIKE %s;",
                    ('%'+values['-NOME-']+'%',))
                resposta = cursor.fetchall()
                lista.clear()
                for i in range(len(resposta)):
                    lista.append( list(resposta[i]) )
                sg.popup('Quantidade de registros: ' + str(len(resposta)))
                indice = 0
                atualiza()
    elif event == "-TODOS-":
        todos()
    elif event == "->>-":
        indice += 1
        if indice >= len(lista): indice = len(lista)-1
        atualiza()
    elif event == "-<<-":
        indice -= 1
        if indice <= 0: indice = 0
        atualiza()

window.close()

# Fazer as mudanças para a base persistente
con.commit()

# Fechar a comunicação com o servidor
cursor.close()
con.close()
