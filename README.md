# Coleta-de-dados com API Twitter

consumer_key= 'SENHA'
consumer_secret = 'SENHA'
access_token = 'SENHA'
access_token_secret = 'SENHA'
auth = tw.OAuthHandler(consumer_key, consumer_secret)
auth.set_access_token(access_token, access_token_secret)
api = tw.API(auth)

#Todas as palavras com o termo "petrobras"
snscrape twitter-search "petrobras since:2015-07-01 until:2015-09-01 lang:pt -filter:retweets -filter:replies"> petr.txt
#Postagens de uma conta oficial
snscrape twitter-search "from:jairbolsonaro since:2020-10-01 until:2020-10-05 lang:pt -filter:retweets"> teste.txt

#Abrir e ler o TXT que estão os links#
tweet_url = pd.read_csv("teste.txt", index_col= None, header = None, names = ["links"])
tweet_url.head()

#Pegar cada link#
af = lambda x: x["links"].split("/")[-1]
tweet_url['id'] = tweet_url.apply(af, axis=1)

#Transformar tudo em lista#
ids = tweet_url ['id']. tolist ()

#Para "enganar" o twitter, os links foram separados em lotes para não gerar erro#
total_count = len (ids)
chunks = (total_count - 1) // 50 + 1

#Função que extrai as informações de cada tweet e inclui no dataframe/.csv#
def fetch_tw(ids):
    list_of_tw_status = api.statuses_lookup(ids, tweet_mode= "extended")
    empty_data = pd.DataFrame()
    for status in list_of_tw_status:
            tweet_elem = {"tweet_id": status.id,
                     "screen_name": status.user.screen_name,
                     "tweet":status.full_text,
                     "date":status.created_at,
                     "favourite_count": status.favorite_count,
                     "retweet_count": status.retweet_count}
            empty_data = empty_data.append(tweet_elem, ignore_index = True)
    empty_data.to_csv("teste1.csv", mode="a", header=False)


#Um loop dentro de outro loop. Isso foi feito para segmentar mais ainda e evitar mensagens de erro#
#Dessa forma, só serão processadas 50 entradas/links/tweets em cada loop#
for i in range(chunks):
        batch = ids[i*50:(i+1)*50]
        result = fetch_tw(batch)

#Organização do csv: data, favoritos, retweets, usuário, texto, id#
