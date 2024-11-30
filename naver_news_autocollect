import requests
from bs4 import BeautifulSoup
from datetime import datetime
import pytz
import json

# 언론사 oid 목록
press_oid_list = {
    '조선일보': '023', '중앙일보': '025', '동아일보': '020', '한겨레': '028',
    '경향신문': '032', '오마이뉴스': '047', 'KBS': '056', 'MBC': '214',
    'SBS': '055', 'TV조선': '448', 'JTBC': '437', '채널A': '449', 'MBN': '057'
}

# /persistent 디렉토리에 저장
file_path = '/persistent/mnaver_news_collect.json'

def collect_news():
    result_data = []
    
    for news_org, oid in press_oid_list.items():
        try:
            news_url = f"https://media.naver.com/press/{oid}"
            headers = {'User-Agent': 'Mozilla/5.0'}
            response = requests.get(news_url, headers=headers)
            soup = BeautifulSoup(response.text, 'html.parser')
            
            # 현재 시간 (KST)
            kst = pytz.timezone('Asia/Seoul')
            kst_dt = datetime.now(kst)
            tm = kst_dt.strftime("%Y-%m-%d %H:%M:%S")
            
            # 기사 추출
            results = soup.select('a.press_news_link._es_pc_link')
            for result in results:
                if 'main' in result['href']:
                    title = result.select_one('span.press_news_text').text.strip()
                    url = result['href']
                    result_data.append({
                        "media": news_org,
                        "time": tm,
                        "title": title,
                        "url": url
                    })
        except Exception as e:
            # 오류가 발생할 경우 로그에 저장
            result_data.append({
                "media": news_org,
                "time": datetime.now().strftime("%Y-%m-%d %H:%M:%S"),
                "title": "Error",
                "url": str(e)
            })
    
    # 기존 데이터 읽기
    try:
        with open(file_path, 'r', encoding='utf-8') as f:
            existing_data = json.load(f)
    except FileNotFoundError:
        existing_data = []
    
    # 기존 데이터에 새 데이터 추가
    all_data = existing_data + result_data

    # JSON 파일로 저장
    with open(file_path, 'w', encoding='utf-8') as f:
        json.dump(all_data, f, ensure_ascii=False, indent=4)
    
    print(f"뉴스 수집 완료. 결과가 {file_path}에 저장되었습니다.")

# 뉴스 수집 실행
if __name__ == "__main__":
    collect_news()

