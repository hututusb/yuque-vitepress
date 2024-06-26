lora微调
```
cd LLaMA-Factory/data

touch weitiao.json
#weitiao.json文件

```
```
#获取hash值
import hashlib
def calculate_sha1(file_path):
    sha1 = hashlib.sha1()
    try:
        with open(file_path, 'rb') as file:
            while True:
                data = file.read(8192)  # Read in chunks to handle large files
                if not data:
                    break
                sha1.update(data)
        return sha1.hexdigest()
    except FileNotFoundError:
        return "File not found."
 
# 使用示例
file_path = '/mnt/workspace/LLaMA-Factory/data/weitiao.json'  # 替换为您的文件路径
sha1_hash = calculate_sha1(file_path)
print("SHA-1 Hash:", sha1_hash)

```
```
#修改dataset_info.json
#添加
"weitiao":{
"file_name":"weitiao.json",
"file_sha1":"........",
}
```
