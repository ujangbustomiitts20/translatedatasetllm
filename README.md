Dokumentasi Skrip: Terjemahan Dataset .parquet dari Bahasa Inggris ke Bahasa Indonesia

Skrip ini digunakan untuk menerjemahkan kolom teks dari file parquet (biasanya hasil preprocessing dataset) yang berisi data dalam Bahasa Inggris, seperti kolom question dan multiple_choice_answer,
ke dalam Bahasa Indonesia. Proses penerjemahan menggunakan pipeline translation dari model Helsinki-NLP/opus-mt-en-id yang disediakan oleh Hugging Face Transformers.

 Dependensi
Skrip ini membutuhkan beberapa pustaka berikut:

pip install pandas transformers torch tqdm
Struktur File
Input: Folder berisi file .parquet dengan kolom teks Bahasa Inggris.

Output: Folder baru berisi file .parquet hasil terjemahan ke Bahasa Indonesia.

Fungsi-Fungsi Utama
1. translate_batch(text_list, desc="")
Menerjemahkan list teks Bahasa Inggris ke Bahasa Indonesia dalam bentuk batch.

Parameter:

text_list: List teks yang akan diterjemahkan.

desc: Deskripsi yang ditampilkan di progress bar.

Return: List teks hasil terjemahan.

translated = translate_batch(df["question"].tolist(), desc="Menerjemahkan 'question'")
2. translate_parquet_file(input_file, output_file)
Membaca file .parquet, menerjemahkan kolom tertentu, dan menyimpan hasilnya ke file .parquet baru.

Parameter:

input_file: Path file input .parquet

output_file: Path file output hasil terjemahan

Kolom yang diterjemahkan:

question

multiple_choice_answer

Bagian Utama: if __name__ == "__main__":
Argument Command Line
Skrip dapat dijalankan via terminal dengan argumen berikut:

--input_dir: Folder yang berisi file .parquet

--output_dir: Folder tempat menyimpan hasil terjemahan

--use_gpu: Gunakan GPU jika tersedia (--use_gpu bersifat opsional)

Contoh penggunaan:


python translate_parquet.py --input_dir ./data/original --output_dir ./data/translated --use_gpu
Pipeline Translator
Membuat pipeline penerjemah:

translator = pipeline("translation", model="Helsinki-NLP/opus-mt-en-id", device=device)
device = 0: Gunakan GPU
device = -1: Gunakan CPU

Proses File
Melakukan iterasi pada semua file .parquet dalam input_dir, lalu memproses dan menyimpan hasilnya ke output_dir.

Catatan Teknis
Batch size default untuk terjemahan adalah 20. Ini bisa diubah untuk menghindari masalah memory jika dataset besar.

max_length=512: Menjamin input ke model tidak melebihi batas panjang token yang didukung.

Skrip menggunakan tqdm untuk menampilkan progress bar interaktif.

Output
Setelah proses selesai, file .parquet hasil terjemahan akan disimpan di folder yang ditentukan di --output_dir. Setiap file memiliki struktur yang sama dengan file aslinya, namun isinya sudah diterjemahkan ke Bahasa Indonesia.
