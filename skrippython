import os
import argparse
import pandas as pd
from transformers import pipeline
import torch
from tqdm import tqdm

def translate_batch(text_list, desc=""):
    """Menerjemahkan list teks Inggris ke Bahasa Indonesia dalam batch"""
    translated = []
    batch_size = 20
    for i in tqdm(range(0, len(text_list), batch_size), desc=desc, leave=False):
        batch = text_list[i:i + batch_size]
        results = translator(
            batch,
            batch_size=batch_size,
            truncation=True,
            max_length=512
        )
        translated.extend([r['translation_text'] for r in results])
    return translated

def translate_parquet_file(input_file, output_file):
    tqdm.write(f" Membaca: {input_file}")
    df = pd.read_parquet(input_file)

    # Translate kolom question dan multiple_choice_answer dengan nested tqdm
    df["question"] = translate_batch(df["question"].tolist(), desc="Menerjemahkan 'question'")
    df["multiple_choice_answer"] = translate_batch(df["multiple_choice_answer"].tolist(), desc="Menerjemahkan 'answer'")

    # Simpan hasil ke file parquet baru
    os.makedirs(os.path.dirname(output_file), exist_ok=True)
    df.to_parquet(output_file, index=False)
    tqdm.write(f" Disimpan ke: {output_file}\n")

if __name__ == "__main__":
    parser = argparse.ArgumentParser()
    parser.add_argument("--input_dir", required=True, help="Folder berisi file parquet asli")
    parser.add_argument("--output_dir", required=True, help="Folder output hasil terjemahan")
    parser.add_argument("--use_gpu", action="store_true", help="Gunakan GPU jika tersedia")
    args = parser.parse_args()

    # Siapkan translator pipeline
    device = 0 if args.use_gpu and torch.cuda.is_available() else -1
    translator = pipeline("translation", model="Helsinki-NLP/opus-mt-en-id", device=device)

    # Proses semua file .parquet di input_dir
    parquet_files = sorted(f for f in os.listdir(args.input_dir) if f.endswith(".parquet"))
    for fname in tqdm(parquet_files, desc="🔄 Menerjemahkan semua file"):
        input_path = os.path.join(args.input_dir, fname)
        output_path = os.path.join(args.output_dir, fname)
        translate_parquet_file(input_path, output_path)

    tqdm.write("Semua file selesai diterjemahkan.")

eksekusi program
venvtrainingllm) openai@openai:~/trainingllm/dataset/dataset$ python terjemahversi3.py \
  --input_dir "/home/openai/.cache/huggingface/hub/datasets--merve--vqav2-small/snapshots/4b070c6254225a7355070c94e14c3275606f521d/data" \
  --output_dir "./vqav2_translated_parquet" \
  --use_gpu



