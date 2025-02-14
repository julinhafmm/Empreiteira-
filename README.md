import sqlite3
from datetime import datetime

def criar_tabela():
    conn = sqlite3.connect("vendas_blocos.db")
    cursor = conn.cursor()
    cursor.execute('''
        CREATE TABLE IF NOT EXISTS vendas (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            cliente TEXT NOT NULL,
            local TEXT NOT NULL,
            data TEXT NOT NULL,
            horario TEXT NOT NULL,
            quantidade INTEGER NOT NULL
        )
    ''')
    conn.commit()
    conn.close()

def registrar_venda():
    cliente = input("Nome do cliente: ")
    local = input("Local da entrega: ")
    data = input("Data (YYYY-MM-DD): ")
    horario = input("Horário (HH:MM): ")
    quantidade = int(input("Quantidade de blocos: "))
    
    conn = sqlite3.connect("vendas_blocos.db")
    cursor = conn.cursor()
    cursor.execute('''
        INSERT INTO vendas (cliente, local, data, horario, quantidade)
        VALUES (?, ?, ?, ?, ?)
    ''', (cliente, local, data, horario, quantidade))
    conn.commit()
    conn.close()
    print("Venda registrada com sucesso!\n")

def consultar_vendas():
    conn = sqlite3.connect("vendas_blocos.db")
    cursor = conn.cursor()
    opcao = input("Consultar por (1) Cliente, (2) Data, (3) Local: ")
    
    if opcao == "1":
        cliente = input("Nome do cliente: ")
        cursor.execute("SELECT * FROM vendas WHERE cliente = ?", (cliente,))
    elif opcao == "2":
        data = input("Data (YYYY-MM-DD): ")
        cursor.execute("SELECT * FROM vendas WHERE data = ?", (data,))
    elif opcao == "3":
        local = input("Local: ")
        cursor.execute("SELECT * FROM vendas WHERE local = ?", (local,))
    else:
        print("Opção inválida.")
        conn.close()
        return
    
    vendas = cursor.fetchall()
    conn.close()
    
    if vendas:
        for venda in vendas:
            print(f"ID: {venda[0]}, Cliente: {venda[1]}, Local: {venda[2]}, Data: {venda[3]}, Horário: {venda[4]}, Quantidade: {venda[5]}")
    else:
        print("Nenhuma venda encontrada.")

def gerar_relatorio_mensal():
    mes = input("Digite o mês (MM): ")
    ano = input("Digite o ano (YYYY): ")
    conn = sqlite3.connect("vendas_blocos.db")
    cursor = conn.cursor()
    cursor.execute("SELECT * FROM vendas WHERE data LIKE ?", (f"{ano}-{mes}%",))
    vendas = cursor.fetchall()
    conn.close()
    
    if vendas:
        print(f"Relatório de Vendas para {mes}/{ano}:\n")
        for venda in vendas:
            print(f"Cliente: {venda[1]}, Local: {venda[2]}, Data: {venda[3]}, Horário: {venda[4]}, Quantidade: {venda[5]}")
    else:
        print("Nenhuma venda encontrada para esse período.")

def menu():
    criar_tabela()
    while True:
        print("\nSistema de Gerenciamento de Vendas de Blocos")
        print("1. Registrar Venda")
        print("2. Consultar Vendas")
        print("3. Gerar Relatório Mensal")
        print("4. Sair")
        opcao = input("Escolha uma opção: ")
        
        if opcao == "1":
            registrar_venda()
        elif opcao == "2":
            consultar_vendas()
        elif opcao == "3":
            gerar_relatorio_mensal()
        elif opcao == "4":
            print("Saindo...")
            break
        else:
            print("Opção inválida. Tente novamente.")

if __name__ == "__main__":
    menu()
