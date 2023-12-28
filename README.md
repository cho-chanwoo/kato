# kato
# First, ensure you have the necessary packages installed:
 #pip install flask flask-ngrok selenium
import ssl
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from flask import Flask, request, jsonify
from flask_ngrok import run_with_ngrok

# # Chrome 옵션 설정
# chrome_options = webdriver.ChromeOptions()
# chrome_options.add_argument('--headless')  # For running headless mode
# chrome_options.add_argument('--no-sandbox')
# chrome_options.add_argument('--disable-dev-shm-usage')

# # Local 환경에서 Chrome 드라이버 경로 설정
# # 이 경로는 로컬 시스템에 설치된 Chrome 드라이버의 위치에 맞게 수정해야 합니다.
# driver = webdriver.Chrome(options=chrome_options)

app = Flask(__name__)
# run_with_ngrok(app)  # Ngrok을 Flask 애플리케이션에 추가

@app.route('/send_message', methods=['POST'])
def send_message():
    user_message = request.json['message']
    response = chat_with_bot(user_message)
    return jsonify({'response': response})

def chat_with_bot(user_message):
    try:
        # 챗봇 웹사이트에 접속 및 사용자 메시지 전송
        driver.get("https://chat.openai.com/g/g-AVQAIMDWr-kato-megumi")

        # 입력 필드 XPath 설정
        input_xpath = '//*[@id="prompt-textarea"]'
        WebDriverWait(driver, 10).until(EC.element_to_be_clickable((By.XPATH, input_xpath)))
        input_field = driver.find_element(By.XPATH, input_xpath)
        input_field.clear()
        input_field.send_keys(user_message + "\n")

        # 챗봇 응답 수집 로직 (적절한 XPath 사용)
        response_xpath = '//*[contains(@class, "response")]'
        WebDriverWait(driver, 10).until(EC.visibility_of_element_located((By.XPATH, response_xpath)))
        response = driver.find_element(By.XPATH, response_xpath).text

        return response
    except Exception as e:
        return str(e)

if __name__ == '__main__':
    app.run(debug=True, ssl_context='adhoc')
