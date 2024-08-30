import tweepy
import requests
import smtplib
from email.mime.multipart import MIMEMultipart
from email.mime.text import MIMEText
from email.mime.image import MIMEImage
from io import BytesIO

# Configuration des clés d'API Twitter
API_KEY = '4nbufE4dNTcxH5CLXQpoSqEt8'# LA TU MET TOUT CE QUI EST API ET CLES 
API_SECRET_KEY = 'NOkJpvLraUZ6Cxwr7kqArKIbCGlpuJUXGgfNUkJcCedQ2kOFM6'
ACCESS_TOKEN = '790530753666879488-hSIAJQbcfe5TUDmTRSANdbKmBHCDSzm'
ACCESS_SECRET = 'CLqMWoMSqyCoXuugipcr4BdoYtrQzSHFEU2fxUlDKf0qQ'

# Configuration de l'email
EMAIL_ADDRESS = 'ghassbenab@gmail.com'
EMAIL_PASSWORD = 'Ghassen2002'
RECIPIENT_EMAIL = 'benabdeljelil2012@gmail.com'

# Authentification à l'API Twitter
auth = tweepy.OAuth1UserHandler(API_KEY, API_SECRET_KEY, ACCESS_TOKEN, ACCESS_SECRET)
api = tweepy.API(auth)

# Récupération des derniers tweets d'un compte spécifique
username = 'ActuFoot_ ' # LA TU MET LE AROBASE DU COMPTE 
tweets = api.user_timeline(screen_name=username, count=10, tweet_mode='extended')

# Création du message email
msg = MIMEMultipart()
msg['From'] = EMAIL_ADDRESS
msg['To'] = RECIPIENT_EMAIL
msg['Subject'] = 'Images de Twitter'

# Ajouter du texte au message
msg.attach(MIMEText('Voici les images collectées depuis le compte Twitter.', 'plain'))

# Téléchargement des images et ajout au message
for tweet in tweets:
    media = tweet.entities.get('media', [])
    if len(media) > 0:
        for image in media:
            image_url = image['media_url']
            response = requests.get(image_url)
            image_data = BytesIO(response.content)
            img = MIMEImage(image_data.read(), name='image.jpg')
            msg.attach(img)

# Envoi de l'email
with smtplib.SMTP('smtp.example.com', 587) as server:
    server.starttls()
    server.login(EMAIL_ADDRESS, EMAIL_PASSWORD)
    server.send_message(msg)

print('Email envoyé c carré.')
