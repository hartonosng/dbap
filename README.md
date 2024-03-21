# dbap

import streamlit as st
import pandas as pd
from fuzzywuzzy import process

# Load dataframe
df_appraisal = pd.read_csv("data_appraisal.csv")

# Main function to filter dataframe based on user input using fuzzy matching
def filter_data(df, search_query):
    # Create a list of all possible addresses from the dataframe
    all_addresses = df['kelurahan'].tolist() + df['kecamatan'].tolist() + df['kota'].tolist() + df['provinsi'].tolist()
    
    # Use fuzzy matching to find the best match
    best_match = process.extractOne(search_query, all_addresses)

    if best_match:
        matched_address = best_match[0]
        st.write(f"Alamat Terdekat: {matched_address}")

        # Filter dataframe based on the best matched address
        filtered_df = df[(df['kelurahan'] == matched_address) | (df['kecamatan'] == matched_address) |
                         (df['kota'] == matched_address) | (df['provinsi'] == matched_address)]
        return filtered_df
    else:
        st.write("Alamat tidak ditemukan.")
        return None

# Streamlit app
def main():
    st.title("Pencarian Properti")

    # Input field for searching
    search_query = st.text_input("Masukkan Alamat:")

    if st.button("Cari"):
        # Filter data based on user input
        filtered_df = filter_data(df_appraisal, search_query)

        if filtered_df is not None:
            # Show filtered results
            st.write("Hasil Pencarian:")
            st.write(filtered_df)

if __name__ == "__main__":
    main()
