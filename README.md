import requests
import json

# Yeni bir geçici e-posta adresi oluşturma
def create_temp_mail():
    response = requests.get("https://api.mail.tm/domains")
    domain = response.json()['hydra:member'][0]['domain']
    username = "random_" + str(random.randint(1000, 9999))
    email_address = f"{username}@{domain}"
    
    # Hesap oluşturma
    account_data = {
        "address": email_address,
        "password": "password123"
    }
    response = requests.post("https://api.mail.tm/accounts", json=account_data)
    
    if response.status_code == 201:
        print(f"Geçici e-posta adresi oluşturuldu: {email_address}")
        return email_address, account_data['password']
    else:
        print("E-posta adresi oluşturulamadı.")
        return None, None

# Gelen e-postaları okuma
def get_inbox(email, password):
    session_data = {"address": email, "password": password}
    response = requests.post("https://api.mail.tm/token", json=session_data)
    token = response.json().get('token')
    
    headers = {"Authorization": f"Bearer {token}"}
    inbox_response = requests.get("https://api.mail.tm/messages", headers=headers)
    messages = inbox_response.json()['hydra:member']
    
    for message in messages:
        print(f"Gönderen: {message['from']['address']}")
        print(f"Konu: {message['subject']}")
        print(f"İçerik: {message['intro']}")
        print("----------")

# Kullanım
email, password = create_temp_mail()
if email:
    get_inbox(email, password)
