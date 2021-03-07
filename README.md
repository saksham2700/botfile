 flask import Flask, request
import requests
from twilio.twiml.messaging_response import MessagingResponse

app = Flask(__name__)

@app.route('/')
def hello():
    return "Welcome Your BOT, It has Successfully BORN OUT ! "

@app.route('/bot', methods=['POST'])
def bot():
    incoming_msg = request.values.get('Body', '').lower()
    resp = MessagingResponse()
    msg = resp.message()
    flag = False
    if 'joke' in incoming_msg:
        # return a joke
        r = requests.get('https://official-joke-api.appspot.com/random_joke')
        if r.status_code == 200:
            data = r.json()
            quote = f'\n {data["setup"]} \n("{data["punchline"]}")'
        else:
            quote = 'I could not retrieve a joke at this time, sorry.'
        msg.body(quote)
        flag = True 
      
      
    if 'pic' in incoming_msg:
        # return a cat pic
        msg.media('https://cataas.com/cat')
        flag = True 
    if 'dice' in incoming_msg:
        # return a quote
        r = requests.get('http://roll.diceapi.com/json/d6')
        if r.status_code == 200:
            data = r.json()
            quote = f'\n"{data["dice"][0]["value"]}"'
        else:
            quote = 'I could not roll a dice at this time, sorry.'
        msg.body(quote)
        flag = True
    if 'report of' in incoming_msg:
        # return a quote
        sen,city =incoming_msg.split('report of ')
        url="http://api.openweathermap.org/data/2.5/weather?q="+city+"&units=metric&appid=d4f0b55b9160189ce2f26a3b4e89bb41"
        r = requests.get(url)
        if r.status_code == 200:
            data = r.json()
            quote = f' temp : {data["main"]["temp"]} \n  temp_min : {data["main"]["temp_min"]} \n temp_max : {data["main"]["temp_max"]} \n pressure : {data["main"]["pressure"]} \n humidity : {data["main"]["humidity"]}'
        else:
            quote = 'try later.'
        msg.body(quote)
        flag = True      
    if 'meme' in incoming_msg:
        # return a quote
        sen,meme =incoming_msg.split('meme ')
        name,gender=meme.split(',')
        x = name.replace(" ", "%20")
        url_p="https://belikebill.ga/billgen-API.php?default=1&name="+x+"&sex="+gender
        msg.media(url_p)
        flag = True 
        
    if 'currency rates' in incoming_msg:
        # return a quote
        post_data = {'format':'json', 'api_key':'[YOUR_API_KEY]','currency':'USD'}
 
        response = requests.post('https://api.iban.com/clients/api/currency/rates/',post_data)
        print(response.text)
        
        
        
        
        
        
             
    if not flag:
        msg.body('Hey I am learning and growing day by day soon I will be capable to answer that \n- *Be a nerd BOT*')
    return str(resp)


if __name__ == '__main__':
    app.run()
