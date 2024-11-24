import requests
from bs4 import BeautifulSoup
import random
import sys
import os
import string
from datetime import datetime
import time
import traceback
import names

def random_ua_desktop():
    with open('useragent.txt', 'r') as f:
        user_agents = f.read().splitlines()
    return random.choice(user_agents)

def generate_random_name():
    first_name = names.get_first_name()
    last_name = names.get_last_name()
    return first_name, last_name

def generate_random_email():
    chars = string.ascii_lowercase + string.digits
    length = random.randint(8, 12)
    username = ''.join(random.choice(chars) for _ in range(length))
    return f"{username}@nexar.my.id"

def generate_random_password():
    lower = string.ascii_lowercase
    upper = string.ascii_uppercase
    digits = string.digits
    symbols = '!@#$%^&*'
    
    # Ensure at least one of each required character type
    pwd = [
        random.choice(lower),
        random.choice(upper),
        random.choice(digits),
        random.choice(symbols)
    ]
    
    # Fill remaining length with random characters
    all_chars = lower + upper + digits + symbols
    pwd.extend(random.choice(all_chars) for _ in range(8))
    random.shuffle(pwd)
    return ''.join(pwd)

def scrape_fb_reg():
    url = 'https://www.facebook.com/reg/'
    
    headers = {
        'authority': 'www.facebook.com',
        'accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8',
        'accept-encoding': 'gzip, deflate, br',
        'accept-language': 'en-US,en;q=0.9',
        'cache-control': 'max-age=0',
        'sec-ch-prefers-color-scheme': 'light',
        'sec-ch-ua': '"Not A(Brand";v="99", "Google Chrome";v="121", "Chromium";v="121"',
        'sec-ch-ua-mobile': '?0',
        'sec-ch-ua-platform': '"Windows"',
        'sec-fetch-dest': 'document',
        'sec-fetch-mode': 'navigate', 
        'sec-fetch-site': 'none',
        'sec-fetch-user': '?1',
        'upgrade-insecure-requests': '1',
        'user-agent': random_ua_desktop()
    }

    cookies = {
        'locale': 'en_US'
    }

    try:
        session = requests.Session()
        response = session.get(url, headers=headers, cookies=cookies)
        
        # Save raw response first for debugging
        with open('raw_response.html', 'w', encoding='utf-8') as f:
            f.write(response.text)
            
        soup = BeautifulSoup(response.text, 'html.parser')

        print("\n=== FACEBOOK REGISTRATION FORM ===\n")
        
        # Try to find registration form
        reg_box = soup.find(['div', 'form'], {'id': ['reg_box', 'reg', 'registration_container']})
        if not reg_box:
            print("[!] Registration form not found")
            print("[i] Response code:", response.status_code)
            print("[i] Content length:", len(response.text))
            print("[i] Raw response saved to raw_response.html")
            return

        # 1. Name Data
        print("Name Data:")
        name_inputs = reg_box.find_all('input', {'type': 'text'})
        for inp in name_inputs:
            if 'name' in inp.attrs:
                print(f"- {inp.get('placeholder', 'No placeholder')} ({inp.get('name')})")
        
        # 2. Birth Date
        print("\nBirth Date:")
        selects = reg_box.find_all('select')
        for sel in selects:
            if sel.get('name') == 'birthday_month':
                print("\nMonth Options:")
                options = sel.find_all('option')
                for opt in options:
                    value = opt.get('value', '')
                    text = opt.text.strip()
                    selected = "✓" if opt.get('selected') else " "
                    print(f"- [{selected}] {text} (value: {value})")
                    
            elif sel.get('name') == 'birthday_year':
                print("\nYear Options:")
                options = sel.find_all('option')
                years = []
                for opt in options:
                    value = opt.get('value', '')
                    selected = "✓" if opt.get('selected') else " "
                    years.append(f"- [{selected}] {value}")
                # Show year range
                if years:
                    first_year = years[-1].split()[-1]
                    last_year = years[0].split()[-1]
                    print(f"Range: {first_year} - {last_year}")
                    print("Selected year:", [y.split()[-1] for y in years if "✓" in y][0])
        
        # 3. Contact & Password
        print("\nContact & Password:")
        contact_inputs = reg_box.find_all('input', {'type': ['text', 'password', 'email']})
        for inp in contact_inputs:
            if 'reg_' in inp.get('name', ''):
                print(f"- {inp.get('placeholder', 'No placeholder')} ({inp.get('name')})")

        # 4. Gender
        print("\nGender:")
        gender_inputs = reg_box.find_all('input', {'type': 'radio', 'name': 'sex'})
        gender_values = {
            '1': 'Female',
            '2': 'Male',
            '-1': 'Custom'
        }
        for inp in gender_inputs:
            value = inp.get('value', '')
            if value in gender_values:
                print(f"- {gender_values[value]} (value: {value})")

        # Generate random data
        first_name, last_name = generate_random_name()
        email = generate_random_email()
        password = generate_random_password()
        gender = random.choice(['1', '2'])
        birth_year = random.randint(1998, 2005)
        birth_month = random.randint(1, 12)
        birth_day = random.randint(1, 28)

        # Get required tokens
        fb_dtsg = soup.find('input', {'name': 'fb_dtsg'})['value'] if soup.find('input', {'name': 'fb_dtsg'}) else ''
        jazoest = soup.find('input', {'name': 'jazoest'})['value'] if soup.find('input', {'name': 'jazoest'}) else ''
        lsd = soup.find('input', {'name': 'lsd'})['value'] if soup.find('input', {'name': 'lsd'}) else ''

        # Update form data
        form_data = {
            'jazoest': jazoest,
            'fb_dtsg': fb_dtsg,
            'lsd': lsd,
            'firstname': first_name,
            'lastname': last_name,
            'reg_email__': email,
            'reg_email_confirmation__': email,
            'reg_passwd__': password,
            'birthday_day': birth_day,
            'birthday_month': birth_month,
            'birthday_year': birth_year,
            'sex': gender,
            'did_use_age': '0',
            'custom_gender': '',
            'websubmit': '1',
            'referrer': '',
            'ns': '0',
            'app_id': '',
            'button_id': 'u_0_s_hI',
            'signup_layout': 'layout|birthday_input_container',
            'is_birthday_layout': '1',
            'birthday_layout': '1',
            'reg_instance': random.randint(10000000, 99999999),
            'locale': 'en_US',
            'client_id': f"{random.randint(100000000000000, 999999999999999)}",
            'reg_click_time': int(time.time()),
            'reg_fb_gate': '',
            'reg_fb_ref': '',
            'reg_social_site': '',
            'submission_request': 'true',
            'encpass': f"#PWD_BROWSER:0:{int(time.time())}:{password}",
            'field_names[]': ['firstname', 'lastname', 'reg_email__', 'reg_email_confirmation__', 'reg_passwd__', 'birthday_day', 'birthday_month', 'birthday_year', 'sex'],
            'reg_impression_id': f"reg_impression_{int(time.time())}",
        }

        # Update headers for POST request
        headers.update({
            'content-type': 'application/x-www-form-urlencoded',
            'origin': 'https://www.facebook.com',
            'referer': url,
            'x-fb-lsd': lsd
        })

        print("\n=== REGISTRATION DATA ===")
        print(f"Name: {first_name} {last_name}")
        print(f"Email: {email}")
        print(f"Password: {password}")
        print(f"Gender: {'Female' if gender == '1' else 'Male'}")
        print(f"Birth Date: {birth_day}/{birth_month}/{birth_year}")
        print("\nAttempting registration...")

        # Simulate human delay
        time.sleep(random.uniform(2, 4))

        # Get correct form action
        reg_form = soup.find('form', {'id': 'reg'})
        if not reg_form:
            print("[!] Registration form not found")
            return
            
        form_action = reg_form.get('action', '')
        if not form_action:
            print("[!] Form action not found")
            return

        try:
            register_response = session.post(
                form_action,
                data=form_data,
                headers=headers,
                cookies=cookies,
                allow_redirects=True
            )
            
            # Save cookies after request
            with open('cookies.txt', 'w') as f:
                f.write(str(dict(session.cookies)))
            
            print(f"\nStatus Code: {register_response.status_code}")
            
            # Save response for debugging
            with open('register_response.html', 'w', encoding='utf-8') as f:
                f.write(register_response.text)
            
            soup_response = BeautifulSoup(register_response.text, 'html.parser')
            
            # Check various response possibilities
            if register_response.status_code == 200:
                # Check error message
                error_msg = soup_response.find(['div', 'span'], {'id': ['reg_error', 'registration_error', 'error_box']})
                if error_msg:
                    print(f"[!] Error: {error_msg.text.strip()}")
                
                # Check redirect URL
                if 'checkpoint' in register_response.url:
                    print("[!] Account created but requires verification")
                    print(f"Redirect URL: {register_response.url}")
                elif 'confirm' in register_response.url:
                    print("[!] Email confirmation required")
                    print(f"Redirect URL: {register_response.url}")
                elif 'home.php' in register_response.url:
                    print("[✓] Registration successful!")
                    print(f"Redirect URL: {register_response.url}")
                else:
                    print("[!] Unrecognized response")
                    print(f"Current URL: {register_response.url}")
                    print("Please check register_response.html for details")
            else:
                print(f"[!] Request failed with status code: {register_response.status_code}")
                
        except Exception as e:
            print(f"[!] Error sending form: {str(e)}")
            traceback.print_exc()

    except Exception as e:
        print(f"[!] Error: {str(e)}")
        traceback.print_exc()

if __name__ == '__main__':
    scrape_fb_reg()
