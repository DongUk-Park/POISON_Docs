## Change yelp json dataset to csv file

### user 데이터
``` python
import json
import csv

data = []

with open('./json_data/user.json', 'r', encoding='UTF8') as f:
    lines = f.readlines()
    for i in lines:
        json_data = json.loads(i)
        data.append(json_data)
        if(json_data['user_id']) == "user_id: lzpM_Vf2rKA4ivGtAIOH4w":
            print("찾음")

print(f'data 크기: {len(data)}')

with open('./csv_data/user.csv', 'w', newline='', encoding='UTF8') as csvfile:
    fieldnames = ['user_id', 'name']
    writer = csv.DictWriter(csvfile, fieldnames=fieldnames)

    writer.writeheader()
    for entry in data:
        writer.writerow({
            'user_id': entry['user_id'],
            'name': entry['name']
        })
```
<br><br>


### POI 데이터
``` python
import json
import csv

data = []
with open('./json_data/business.json', 'r', encoding='UTF8') as f:
    lines = f.readlines()
    for i in lines:
        json_data = json.loads(i.strip())
        # if json_data.get('city', "null") != "Philadelphia":
        #     continue
        data.append(json_data)

print(f'data 크기: {len(data)}')

with open('./csv_data/business.csv', 'w', newline='', encoding='UTF8') as csvfile:
    fieldnames = ['business_id', 'name', 'address', 'city', 'latitude', 'longitude']
    writer = csv.DictWriter(csvfile, fieldnames=fieldnames)

    writer.writeheader()
    for entry in data:
        writer.writerow({
            'business_id': entry['business_id'],
            'name': entry['name'],
            'address': entry['address'],
            'city': entry['city'],
            'latitude': entry['latitude'],
            'longitude': entry['longitude']
        })
```
<br><br>


### Review 데이터
``` python
import json
import csv
import time
start_time = time.time()  # 시작 시간 기록

# Philadelphia POI의 Id를 담는 set 생성
# Philadelphia POI의 review만 골라 csv 생성


data = []
# business_ids_set = set()  # business_id를 담을 set

# # Philadelphia POI의 businessId를 담는 set 생성
# with open('./csv_data/business.csv', 'r', encoding='UTF8') as f:
#     reader = csv.DictReader(f)
#     for row in reader:
#         business_ids_set.add(row['business_id'])
# print(f'business_ids_set 크기: {len(business_ids_set)}')

# Philadelphia POI의 review만 골라 csv 생성
with open('./json_data/review.json', 'r', encoding='UTF8') as f:
    lines = f.readlines()
    print(f'review 전체 데이터 크기: {len(lines)}')
    for index, i in enumerate(lines):
        # if index > 20000:
        #     break
        json_data = json.loads(i)
        # if json_data.get('business_id', "-1") in business_ids_set:
        data.append(json_data)

print(f'data 크기: {len(data)}')

with open('./csv_data/review.csv', 'w', newline='', encoding='UTF8') as csvfile:
    fieldnames = ['review_id', 'user_id', 'business_id', 'stars', 'text']
    writer = csv.DictWriter(csvfile, fieldnames=fieldnames)

    writer.writeheader()
    for index, entry in enumerate(data):
        # if index > 20000:
        #     break
        text = entry['text']
        tmp = text.replace("'", "").replace('"', '')
        # if "'" in cleaned_text or '"' in cleaned_text:
            # print(entry['review_id'])
        cleaned_text = f"'{tmp}'"
        writer.writerow({
            'review_id': entry['review_id'],
            'user_id': entry['user_id'],
            'business_id': entry['business_id'],
            'stars': entry['stars'],
            # 'date': entry['date'], # date가 쌈@뽕하게 안 나오고 ###으로 나옴. 나중에 해결해야 함
            'text': cleaned_text
        })

end_time = time.time()  # 종료 시간 기록
elapsed_time = end_time - start_time  # 실행 시간 계산

print(f'데이터 처리 및 CSV 파일 생성에 소요된 시간: {end_time - start_time} 초')
```
