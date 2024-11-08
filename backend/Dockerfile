FROM python:3.11-slim

WORKDIR /app

# Copiar o requirements.txt e instalar as dependências
COPY ./backend/requirements.txt .
RUN pip install -r requirements.txt

# Copiar o código da aplicação
COPY ./backend .

# Copiar o script wait-for-it.sh
COPY ./backend/wait-for-it.sh /wait-for-it.sh
RUN chmod +x /wait-for-it.sh

# Expor a porta
EXPOSE 5000

# Comando para iniciar a aplicação
CMD ["/wait-for-it.sh", "db:3306", "--timeout=60", "--", "python", "app.py"]
