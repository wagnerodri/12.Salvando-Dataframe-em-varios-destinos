# 12.Salvando-Dataframe-em-varios-destinos
Salvando Dataframe em varios destinos usando json, xlsx,csv , parquet e particionando_parquet
from sqlalchemy import create_engine
import pandas as pd
#conexao com banco de dados

dbname   = 'python'
user     = 'postgres'
password = '***'
host     = 'localhost'
port     = '5435' 

# String de conexão
conexao_str = f'postgresql://{user}:{password}@{host}:{port}/{dbname}'
engine = create_engine(conexao_str)

query = """
select 
aeronave
,datavalidade
,operador
,tipouso
,modelo

from public.mapeamento """

df=pd.read_sql_query(query,engine)
df.head(2)



# JSON
pasta_destino_json = r"C:\CURSO-PYTHON\PYTHON - SGBDS - arquivos\Destino\V_OCORRENCIAAMPLA.json"
df.to_json(pasta_destino_json,orient="index")

#index padrao 
#orient      =  orientação do formato'
#lines=True  = separados por quebras de linha  só aceita no orient = records
#tipode de orientação = records,index,columns,values,table


#XLSX
import pandas as pd
import re

# Função para remover caracteres ilegais
def remove_caracteres_invalidos(valor):
    if isinstance(valor, str):
        # Remove caracteres de controle ASCII (0x00 a 0x1F, exceto \n e \t)
        return re.sub(r"[\x00-\x08\x0B-\x1F\x7F]", "", valor)
    return valor

# Aplica a função ao DataFrame inteiro
df = df.applymap(remove_caracteres_invalidos)

# Agora salve normalmente
pasta_destino_excel = r"C:\CURSO-PYTHON\PYTHON - SGBDS - arquivos\Destino\V_OCORRENCIAAMPLA.xlsx"
df.to_excel(pasta_destino_excel, index=False, sheet_name="Base")

print("Arquivo Excel salvo com sucesso após limpeza!")



#CSV
pasta_destino_csv = r"C:\CURSO-PYTHON\PYTHON - SGBDS - arquivos\Destino\V_OCORRENCIAAMPLA.csv"
df.to_csv(pasta_destino_csv,sep=',',index=False)
#sep=  separador (delimitador)


# Defina o caminho do arquivo Parquet
#!pip install pyarrow
#!pip install fastparquet

pasta_destino_parquet = r"C:\CURSO-PYTHON\PYTHON - SGBDS - arquivos\Destino\V_OCORRENCIAAMPLA.parquet"
# Salve o DataFrame em um arquivo Parquet
df.to_parquet(pasta_destino_parquet, index=False,engine='pyarrow')



import pandas as pd
import os

# Exemplo de DataFrame
df = pd.DataFrame({
    "operador": ["O1", "O2", "O1", "O2"],
    "valor": [100, 200, 150, 250]
})

# Caminho de destino para particionamento (somente a pasta, sem a extensão .parquet)
pasta_destino_particionado = r"C:\CURSO-PYTHON\PYTHON - SGBDS - arquivos\Destino\V_OCORRENCIAAMPLA_particionado"

# Garantir que a pasta existe
os.makedirs(pasta_destino_particionado, exist_ok=True)

# Especificar as colunas para particionamento
colunas_particionamento = ['operador']

# Salvando os dados particionados em formato Parquet
df.to_parquet(
    pasta_destino_particionado,
    index=False,
    partition_cols=colunas_particionamento,
    engine='pyarrow'
)

print("Arquivo particionado salvo com sucesso!")
