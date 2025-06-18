    import streamlit as st
    import pandas as pd
    import plotly.express as px
    import plotly.graph_objects as go
    
    # Konfigurasi halaman
    st.set_page_config(page_title="Dashboard PLN", layout="wide")
    
    st.title("⚡ Dashboard Monitoring Tunggakan Pelanggan PLN")
    
    # Load Data
    @st.cache_data
    def load_data():
        df = pd.read_csv("D:\pln_dashboard\data_tunggakan_pln_dummy.csv")
        df["tanggal_tagihan"] = pd.to_datetime(df["tanggal_tagihan"])
        df = df[df["status_tunggakan"] == "Tunggak"]  # Fokus hanya pelanggan yang menunggak
        return df
    
    df = load_data()
    
    # Sidebar Filter
    st.sidebar.header("🔎 Filter")
    unique_rayon = df['rayon'].dropna().unique()
    selected_rayon = st.sidebar.multiselect("Pilih Rayon", unique_rayon, default=unique_rayon)
    
    unique_tahun = sorted(df["tanggal_tagihan"].dt.year.unique())
    selected_tahun = st.sidebar.selectbox("Pilih Tahun", unique_tahun)
    
    # Filter Data
    df_filtered = df[
        (df["rayon"].isin(selected_rayon)) &
        (df["tanggal_tagihan"].dt.year == selected_tahun)
    ]
    
    # Ringkasan Data
    st.markdown("### 📊 Ringkasan Data")
    col1, col2, col3 = st.columns(3)
    col1.metric("Total Pelanggan", f"{df_filtered['id_pelanggan'].nunique():,}")
    col2.metric("Total Tunggakan (Rp)", f"{df_filtered['besar_tagihan'].sum():,.0f}")
    col3.metric("Rata-rata Tunggakan", f"{df_filtered['besar_tagihan'].mean():,.0f}")
    
    st.divider()
    
    # Grafik 1: Trend Tunggakan Bulanan
    st.markdown("#### 🔁 Trend Tunggakan Bulanan")
    monthly_trend = df_filtered.groupby(pd.Grouper(key="tanggal_tagihan", freq="M"))["besar_tagihan"].sum().reset_index()
    fig1 = px.line(monthly_trend, x="tanggal_tagihan", y="besar_tagihan", markers=True,
                   labels={"tanggal_tagihan": "Tanggal", "besar_tagihan": "Jumlah Tunggakan"},
                   title="Perkembangan Tunggakan Tiap Bulan")
    st.plotly_chart(fig1, use_container_width=True)
    
    # Grafik 2: Per Rayon
    st.markdown("#### 🏢 Tunggakan per Rayon")
    df_rayon = df_filtered.groupby("rayon")["besar_tagihan"].sum().reset_index()
    fig2 = px.bar(df_rayon, x="rayon", y="besar_tagihan", text_auto=True,
                  labels={"rayon": "Rayon", "besar_tagihan": "Jumlah Tunggakan"},
                  title="Total Tunggakan per Rayon")
    st.plotly_chart(fig2, use_container_width=True)
    
    # Grafik 3: Distribusi Tunggakan
    st.markdown("#### 👥 Distribusi Jumlah Tunggakan")
    fig3 = px.histogram(df_filtered, x="besar_tagihan", nbins=30,
                        title="Distribusi Nilai Tunggakan", labels={"besar_tagihan": "Jumlah Tunggakan"})
    st.plotly_chart(fig3, use_container_width=True)
    
    # Grafik 4: Top 10 Pelanggan Menunggak
    st.markdown("#### 🔝 Top 10 Pelanggan dengan Tunggakan Tertinggi")
    top10 = df_filtered.groupby("id_pelanggan")["besar_tagihan"].sum().nlargest(10).reset_index()
    fig4 = px.bar(top10, x="id_pelanggan", y="besar_tagihan", text_auto=True,
                  labels={"id_pelanggan": "ID Pelanggan", "besar_tagihan": "Jumlah Tunggakan"},
                  title="10 Pelanggan dengan Tunggakan Tertinggi")
    st.plotly_chart(fig4, use_container_width=True)
    
    # Penjelasan
    with st.expander("📘 Penjelasan Analisis"):
        st.markdown("""
        - **Trend Bulanan**: Menampilkan akumulasi tunggakan per bulan.
        - **Per Rayon**: Menunjukkan rayon mana yang menyumbang tunggakan terbesar.
        - **Distribusi Tunggakan**: Menunjukkan sebaran nominal tunggakan pelanggan.
        - **Top 10 Pelanggan**: Fokus ke pelanggan dengan tunggakan terbesar.
        """)
