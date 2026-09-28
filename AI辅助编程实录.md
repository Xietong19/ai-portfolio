## 1.任务与提示词
要做什么：读生词表CSV → 筛选HSK4级词 → 造练习题。
我的提示词：请帮我写一段Python代码，读取data/生词表.csv，筛选出HSK等级为4的词，并生成练习题。
## 2.AI初版代码
import os
import pandas as pd
# 这里假设你已经按老师要求引入了 weekpath 模块
import weekpath 

def generate_practice():
    # 【AI毛病1】直接写了相对路径，没有使用老师要求的 weekpath.root_path()
    base_dir = weekpath.root_path()
    csv_path = "data/生词表.csv" 
    out_path = "练习.txt"
    
    # 【AI毛病2】读取CSV和写入文件时，没有指定 encoding="utf-8"
    df = pd.read_csv(csv_path)
    
    # 【AI毛病3】列名可能和你的CSV不匹配（假设CSV里是"HSK等级"，AI可能写成"hsk_level"）
    hsk4_words = df[df["hsk_level"] == 4]
    
    with open(out_path, "w") as f:
        for index, row in hsk4_words.iterrows():
            word = row["词语"]
            sentence = row["例句"]
            
            # 生成填空题：把句子中的目标词替换成 ___
            if word in sentence:
                blank_sentence = sentence.replace(word, "___")
                f.write(f"【填空题】{blank_sentence}\n")
            else:
                f.write(f"【填空题】{sentence}\n")
                
    print(f"练习生成完毕！文件保存在：{out_path}")

if __name__ == "__main__":
    generate_practice()
## 3. 我的修改点（≥3条）
 
①  修正列名不匹配：AI 初版代码凭空捏造了列名 hsk_level、词语、例句，运行时持续报错 KeyError。我通过查看 data/生词表.csv 的真实表头，发现只有 词汇、HSK等级、词性、释义、备注，遂逐一修正。
	
② AI 初版代码尝试运行时，出现了 ModuleNotFoundError: No module named 'weekpath' 的报错。说明 AI 在写代码时忽略了项目的基础环境文件位置，最终版我将其合并到根目录的 vocab_tool.py 里并解决了该问题。
 
③ 放弃无效逻辑：我发现 CSV 数据中根本不存在 例句 列，AI 强行生成填空题的逻辑是错误的，因此我选择保留更适合该数据的“按词性分组输出”功能，并将产物路径统一到了 weekpath.root_path。
 
## 4. 最终版 vs 初版差异说明
•AI 哪里漏了：AI 完全脱离实际数据，凭空编造列名和缺失的字段（例句），导致程序无法运行。我通过对照 CSV 实际数据，调整了列名并舍弃了不适用的逻辑。
• AI 哪里想多了：AI 没有考虑数据缺失的情况，硬要生成填空题，我根据实际数据调整了输出格式。
