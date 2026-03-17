# P-L
O script utiliza yfinance para capturar dados em tempo real. Ele processa o ticker, extrai o índice P/L (Preço/Lucro) e gera três subplots com matplotlib para os períodos de 1, 5 e 10 anos. É uma automação que aplica lógica de programação para simplificar a análise fundamentalista, transformando dados brutos em visualizações estratégicas.

import yfinance as yf
import matplotlib.pyplot as plt

def analisar_acao(ticker_input):
    # Configura o ticker (adiciona .SA se for mercado brasileiro e o usuário esquecer)
    if not ticker_input.endswith('.SA') and len(ticker_input) <= 5:
        ticker_symbol = ticker_input.upper() + '.SA'
    else:
        ticker_symbol = ticker_input.upper()

    # Obtém os dados da ação
    acao = yf.Ticker(ticker_symbol)
    
    # 1. Resultado: Preço por Lucro (P/L ou P/E Ratio)
    info = acao.info
    pe_ratio = info.get('forwardPE') or info.get('trailingPE')
    
    print("-" * 30)
    if pe_ratio:
        print(f"Ticker: {ticker_symbol}")
        print(f"Índice Preço/Lucro (P/L): {pe_ratio:.2f}")
    else:
        print(f"Ticker: {ticker_symbol}")
        print("P/L: Não disponível para este ativo no momento.")
    print("-" * 30)

    # 2. Gráficos de 1, 5 e 10 anos
    periodos = ["1y", "5y", "10y"]
    titulos = ["1 Ano atrás", "5 Anos atrás", "10 Anos atrás"]
    
    fig, axes = plt.subplots(1, 3, figsize=(18, 5))
    fig.suptitle(f'Histórico de Preço - {ticker_symbol}', fontsize=16)

    for i, p in enumerate(periodos):
        hist = acao.history(period=p)
        if not hist.empty:
            axes[i].plot(hist.index, hist['Close'], color='blue')
            axes[i].set_title(titulos[i])
            axes[i].set_ylabel('Preço (Fechamento)')
            axes[i].grid(True)
        else:
            axes[i].set_title(f"{titulos[i]} (Sem dados)")

    plt.tight_layout()
    plt.show()

# Execução
if __name__ == "__main__":
    ticker = input("Digite o ticker da ação (ex: PETR4 ou AAPL): ")
    analisar_acao(ticker)
