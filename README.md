import pandas as pd
import streamlit as st
import matplotlib.pyplot as plt

st.title("Análise de Conteúdos da Netflix")

@st.cache_data
def load_data():
    df = pd.read_csv("netflix_titles.csv")
    df['date_added'] = pd.to_datetime(df['date_added'], errors='coerce')
    df['year_added'] = df['date_added'].dt.year
    df.dropna(subset=['year_added'], inplace=True)
    df['year_added'] = df['year_added'].astype(int)
    return df

df = load_data()

anos = sorted(df['year_added'].dropna().unique())
ano = st.selectbox("Selecione o ano de adição:", anos)

df_filtrado = df[df['year_added'] == ano]

st.subheader(f"Dados do ano {ano}")
st.dataframe(df_filtrado[['title', 'type', 'country', 'release_year']])

st.subheader("Quantidade de títulos por ano")
titulos_por_ano = df['year_added'].value_counts().sort_index()
fig1, ax1 = plt.subplots()
ax1.plot(titulos_por_ano.index, titulos_por_ano.values, marker='o')
ax1.set_xlabel("Ano")
ax1.set_ylabel("Número de Títulos")
st.pyplot(fig1)

st.subheader("Tipos de conteúdo")
tipos = df_filtrado['type'].value_counts()
fig2, ax2 = plt.subplots()
ax2.bar(tipos.index, tipos.values)
st.pyplot(fig2)

st.subheader("Dispersão: Ano de lançamento vs. Ano adicionado")
fig3, ax3 = plt.subplots()
ax3.scatter(df_filtrado['release_year'], df_filtrado['year_added'], alpha=0.5)
st.pyplot(fig3)

st.subheader("Top 5 países com mais títulos")
top5 = df_filtrado['country'].value_counts().head(5)
fig4, ax4 = plt.subplots()
ax4.pie(top5.values, labels=top5.index, autopct='%1.1f%%', startangle=90)
ax4.axis('equal')
st.pyplot(fig4)
