# NLP_Study

#### ngram
```
import re
string = "하루 10분 강의로 핵심만 쏙쏙! 지금 바로"

words = string.split()
output = []
ngramNum = 4
targetReg = re.compile("강의\w+")
for i in range(0, len(words)):
    if targetReg.search(words[i]) != None:
        print(words[i])

        fromNum = i - ngramNum
        if fromNum <= 0:
            fromNum = 0

        toNum = i + ngramNum + 1
        if len(words) <= toNum:
            toNum = len(words)
        
        output.append(words[fromNum : toNum])
print(output)
```
```
import re

string = 'hellow my Wame wWissam'
pattern = r'[Ww]+'

words = re.split(r'\s', string)  # 문자열을 단어로 분리
matches = [word for word in words if re.match(pattern, word)]  # 대문자 W나 소문자 w가 포함된 단어만 추출
result = ' '.join(matches)

print(result)  # 'hellow Wame wWissam'
```
### 1 1 Regular Expressions
```
import re
text = """We looked!
Then we saw him step in on the mat.
We looked!
And we saw him!
The Cat in the Hat!"""
print(text)
print(re.findall(r'\b\w*[Ww]\w*\b',text))

text = """We looked!
Then we saw him step in on the mat.
We looked!
And we saw him!
The Cat in the Hat!"""
print(text)
r = re.compile(r'(\w*at|step\w*)')
print(re.findall(r,text))
```
- ?기호 앞에 있는 문자가 존재할 수도 있고 하지 않을 수도 있다.
- *기호 앞에 있는 문자가 0개 이상일 때
-  +기호 앞에 있는 문자가 1개 이상일 때
-  .기호 자리에 어떠한 한 글자라도 올수 있다.
-  ^[] 문자열의 시작부분부터 확인
-  []& 문자열 끝에서부터 확인

### 1 2 Regular Expression Substitutions
```
import re

text = "the faster they ran, the faster we ran"
r = re.compile(r'the (.*)er they (.*), the \1er we \2')

print(re.findall(r,text))
```
- 문자열 전방탐색
- 긍정형 전방탐색 (?= pattern)
- 부정형 전방탐색 (?! pattern)
```
import re
text = "some cats like some cats"

r = re.compile(r'(?:some|a few) (people|cats) like some \1')

print(re.findall(r, text))
```
```
import re

text = "This is a test sentence. Volcano erupted yesterday."
r = re.compile(r'^(?!Volcano)[A-Za-z]+')

print(re.findall(r, text))  # ['This', 'is', 'a', 'test', 'sentence.', 'erupted', 'yesterday.']
```
```
import re

text = "This is a test sentence. Volcano erupted yesterday."
r = re.compile(r'\b(?!Volcano)[A-Za-z]+\b')

print(re.findall(r, text))  # ['This', 'is', 'a', 'test', 'sentence.', 'erupted', 'yesterday.']
```
### 1 3 Words and Corpora

### 1 4 Word Tokenization
```
import re
text = 'Hello, world! Python HTML CSS JAVASCRIPT'
r = re.compile('[^A-Za-z]+')  # 알파벳이 아닌 모든 문자를 선택함, 1개 이상의 문자열을 선택함
result = r.sub('\n', text)    # r에 해당하는 문자열을 '\n'으로 대체하여 결과를 result에 저장
print(result)
```
```
import re

text = """In Israel, thousands of protesters demonstrated Sunday and Monday against Prime Minister Benjamin Netanyahu’s plans to make changes to the nation’s top court. People walked the streets of Tel Aviv and Jerusalem, waving white and blue Israeli flags. Many workers did not go to their jobs on Monday and Ben Gurion Airport near Tel Aviv suspended outgoing flights. The leader of Israel’s largest worker’s group is Arnon Bar-David. “Bring back the country’s sanity,” he said. Netanyahu’s conservative coalition government says Israel’s Supreme Court has too much power. The changes would permit leaders in parliament, known as the Knesset, to appoint judges and also restrict the court’s ability to rule against the government. The plan would give lawmakers greater power in appointing new judges. Those in the conservative government say the court is often putting the rights of minorities ahead of the nation’s interests. The current rule in Israel requires agreement on new judges from a group that includes politicians and judges. The new plan would permit government leaders to name the judges. Netanyahu said the plan would balance the court. Critics, however, say he wants his government to be able to change the group of judges because he is facing an ongoing legal case. Netanyahu is barred from involvement in the planned changes. One member of the government, Yoav Gallant, was dismissed as defense minister for opposing the plan. Some Israelis worried about the country’s armed forces without the top government leader. Netanyahu was supposed to make a television appearance on Monday announcing the changes, but it was postponed. He made a statement on Twitter, asking protesters “to behave responsibly and to not act violently. We are brotherly people,” he said. It is not clear when the Prime Minister will push again for the changes, some think it might be after parliament returns on April 30 after the Jewish Passover holiday."""
pattern = '[^A-Za-z]+'  # 알파벳이 아닌 문자를 선택하는 패턴

r = re.compile('[^A-Za-z]+')
# 문자열 처리 후 개행문자로 분리하여 단어들을 추출
words = r.sub(' ', text).split()

# 각각의 단어별로 count
word_count = {}
for word in words:
    if word in word_count:
        word_count[word] += 1
    else:
        word_count[word] = 1

# 추출한 단어들을 정렬하여 중복 제거
unique_words = sorted(set(words))

# 결과 출력
print("Unique words:", unique_words)
print("Word count:", word_count)
```
```
import nltk
text = 'That U.S.A poster-print costs $12.40. . .'
pattern = '[^A-Za-z]+'
print(nltk.regexp_tokenize(text, pattern))
```
### 1 5 Byte Pair Encoding
```
import re

def get_stats(vocab):
    pairs = {}
    for word, freq in vocab.items():
        symbols = word.split()
        for i in range(len(symbols)-1):
            pair = (symbols[i], symbols[i+1])
            if pair in pairs:
                pairs[pair] += freq
            else:
                pairs[pair] = freq
    return pairs

def merge_vocab(pair, v_in):
    v_out = {}
    bigram = re.escape(' '.join(pair))
    p = re.compile(r'(?<!\S)' + bigram + r'(?!\S)')
    for word in v_in:
        w_out = p.sub(''.join(pair), word)
        v_out[w_out] = v_in[word]
    return v_out

vocab = {'e r e r die roast': 1}
num_merges = 1
for i in range(num_merges):
    pairs = get_stats(vocab)
    if not pairs:
        break
    best = max(pairs, key=pairs.get)
    vocab = merge_vocab(best, vocab)

print(vocab)
```
### 1 6 Word Normalization
- Lemmatization은 표제어를 찾는 과정이다. 형태소를 분석해 파악한다. ('am', 'are', 'is')의 표제어는 'be' ('car', 'cars', "car's"의 표제어는 'car'이다)
- Stemming은 Lemmatization보다 단순하다. 단어의 형태만 보고 판단하기 때문에 'This'를 'Thi'라고 잘못 찾을수도 있다.

### 2 1 Defining Minimum Edit Distance

#### 편집거리 알고리즘
```
def edit_dist(str1, str2):
    dp = [[0] * (len(str2)+1) for _ in range(len(str1) + 1)]
    for i in range(1, len(str1)+1):
        dp[i][0] = i
    for j in range(1, len(str2)+1):
        dp[0][j] = j

    for i in range(1, len(str1)+1):
        for j in range(1, len(str2)+1):
            if str1[i-1] == str2[j-1]:
                dp[i][j] = dp[i-1][j-1]

            else:
                dp[i][j] = min(dp[i-1][j-1], dp[i-1][j], dp[i][j-1]) + 1

    return dp[-1][-1]


print(edit_dist('ATTATC', 'ATCAT'))

```
```
해당 함수는 두 개의 문자열 str1과 str2를 입력으로 받아서 최소 편집 거리를 계산하는 함수입니다.

먼저 m과 n에 각각 str1과 str2의 길이를 저장합니다. 그리고 dp라는 이름의 m+1 x n+1 크기의 2차원 배열을 생성합니다. 이 배열은 다이나믹 프로그래밍의 개념을 사용하여 계산된 최소 편집 거리를 저장할 것입니다.

다음으로, dp의 첫 번째 행과 열을 채웁니다. 이때, 첫 번째 행은 str1의 첫 번째 글자를 삭제해야 하는 경우의 최소 비용을 계산한 값으로 채웁니다. 첫 번째 열은 str2의 첫 번째 글자를 삽입해야 하는 경우의 최소 비용을 계산한 값으로 채웁니다.

그 다음, dp의 나머지 부분을 채워넣습니다. 이때, 만약 str1의 i번째 글자와 str2의 j번째 글자가 같다면, dp[i][j]는 dp[i-1][j-1]의 값과 같습니다. 그렇지 않은 경우에는 dp[i][j]를 dp[i-1][j], dp[i][j-1], dp[i-1][j-1]의 값 중 가장 작은 값에 1을 더한 값으로 설정합니다.

마지막으로, dp의 가장 마지막 값인 dp[m][n]을 반환합니다. 이 값은 최소 편집 거리를 나타냅니다.
```
```
import csv

long_text = """이따금씩 대신들도 이 기술을 국왕에게 보여줌으로써, 아직 자신들이 불타는 것을 잊지 않고 있다는 것을 증명하기도 한다. 재무대신 플리냅은 팽팽하게 당겨진 줄 위에서 춤을 추었는데, 그 왕국의 어떠한 귀족보다도 25밀리미터나 높게 뛰었다. 나는 그가 영국에 있는 노끈보다도 더욱 가 느다란 줄에 고정된 나무접시 위에서 몇 번인가 재주를 넘는 것을 보았 다. 나의 친구인 렐드레살은 플리냅 다음으로 춤을 잘 추었다. 그 이외의 대신들은 대개가 서로 비슷하였다. 이 놀이는 이따금씩 치명적인 사고가 발생하기도 하는데, 정식으로 기록되어 있는 것만으로도 상당수에 달했 다. 춤을 추다가 후보들이 다치는 것을 나도 여러 차례 보았다.  대신들이 직접 묘기를 보일 때가 가장 위험하였다. 주위의 동료들보다 자신이 더욱 뛰어나다는 것을 보이려고 무리하게 뛰다가 한두 번 떨어진 경험이 없는 사람은 거의 없었다. 내가 이곳에 오기 1,2년 전 플리냅이 떨어졌을 때 바닥에 우연히 국왕의 방석이 놓여 있지 않았더라면, 그는 분명히 목이 부러져 죽었을 것이다.  이것과 비슷한 놀이가 또 하나 있었다. 그것은 특별한 경우, 국왕과 왕 비 그리고 중요한 대신들의 앞에서만 펼쳐지는 것이다. 국왕은 탁자 위에 길이가 대략 15센티미터 정도 되는 파란색, 빨간색, 초록색의 비단줄 세 개를 놓는다. 이 비단줄은 특별한 은총의 표시였으며 국왕이 뽑는 사람에 게 상으로 내려주는 것이다. 이러한 예식은 국왕의 의자가 있는 넓은 대 전에서 행하여진다.  여기에서 국왕의 은총을 얻으려는 후보들은 줄타기나 춤추기와는 아주 다른 묘기를 선보인다. 이것은 유럽이나 아메리카 어느 대륙에서도 찾아 볼 수 없었던 것이다. 국왕은 두 손으로 막대기를 잡고 수평이 되도록 만든다. 그러면 후보들이 한 명씩 차례로 나와 막대기가 올라가고 내려가는  것에 따라, 그것을 뛰어넘기도 하고 아래로 기어서 지나가기도 하였다.  때로는 대신들이 반대편을 잡고 있기도 한다.  가장 재치있으면서도, 멀리 뛰거나 기어가는 사람에게 파란색의 비단 줄이 주어진다. 2등에게는 빨간줄 그리고 3등에게 초록줄이 주어진다.  입상자들은 그 실을 허리에 두 번 감는다. 궁중에 있는 고관들은 모두가  이러한 비단줄로 장식을 하고 있었다.  매일같이 국왕과 군사들이 타는 말은 나의 곁에서 훈련을 받았으며, 이 제는 아무런 거리낌이나 두려움 없이 발 아래에까지 다가오고는 하였다. 땅에 세워놓은 손을, 기수들은 말을 타고 뛰어넘었다. 어느 사냥꾼은 커 다란 말을 몰고서 구두를 신은 발을 뛰어넘었는데, 그것은 그들에게 있어 대단한 도약이었다.  어느 날 나는 매우 독특한 방법으로 국왕을 즐겁게 할 수 있는 기회를 마련하였다. 나는 국왕에게 높이가 60 센티미터이고 굵기는 여느 지팡이 만한 몇 개의 막대기를 달라고 하였다.  국왕은 숲을 관할하는 신하에게 나의 요구조건을 그대로 들어주라고  명령하였다. 다음날 아침 여섯 명의 신하들이 여덟 마리의 말이 이끄는 마차 여섯 대를 끌고 왔다. 나는 아홉 개의 막대기를 땅에다 단단하게 박 아서 한 변의 길이가 75센티미터인 정사각형 모양을 만들었다. 그러고는 다른 네 개의 막대기를 이용하여 땅에서 약 60 센티미터 높이로 네 군데 의 귀퉁이에 평행하도록 묶었다. 아홉 개의 막대기 위에다가 손수건을 대  놓고 팽팽하게 잡아당겨서 북처럼 탄탄하도록 만들었다. 손수건으로부터 12센티미터 위쪽에 나 있는, 평행으로 설치한 네 개의 막대기가 난간의  역할을 하도록 만든 것이다.  작업을 마친 다음, 나는 국왕에게 가장 잘 달리는 말 스물 네 마리로 구  44  성된 군대를 불러와 손수건 위에서 훈련을 하도록 제안하였다. 그는 나의 세안을 받아들였다.  나는 무장한 군사가 타고 있는 말을 하나씩 들어서 손수건 위에다가 올 려 주었다. 훈련을 맡게 될 장교도 함께 있었다. 정렬을 하고난 다음 그들 은 두 모임으로 나뉘어 촉이 달려 있지 않은 화살을 쏘고, 칼을 뽑아서 쫓 고 쫓기며, 공격과 후퇴를 하면서 군사훈련을 시작하였다. 나는 이제까지 구경한 것 중에서 가장 뛰어난 군사훈련을 보았다. 평행으로 나 있는 막 대기가 난간 역할을 훌륭하게 해주어서 말들이 아래로 떨어지는 것을 막  아주었다.  국왕은 무척이나 즐거워하면서 훈련을 며칠동안이나 계속하도록 하였 다. 한 번은 직접 무대에 올라가서 지휘를 하기도 하였다. 그리고 억지로 왕비를 설득하여 그녀가 타고 있는 가마를 문이 닫힌 그대로 나에게 들린 채로, 무대에서 2미터 정도 떨어진 거리에서 군사훈련을 지켜볼 수 있도 록 하였다.  그 동안 좋지 않은 사고가 일어나지 않은 것은 진정으로 다행이었다. 다만 어느 대장이 타고 있던 사나운 말이 뒷발질을 하는 바람에 손수건에 구멍을 뚫리게 되었다. 그 구멍 속으로 말의 다리가 빠졌기 때문에 기수 와 함께 넘어졌다. 그러나 나는 한 손으로 구멍을 막고 다른 손으로 그들 을 하나씩 조심스럽게 내려 놓아서 모두 구해 주었다. 넘어진 말은 왼쪽 의 어깨부분을 다쳤지만, 말에 타고 있던 기수는 다행스럽게 다치지 않았 다. 나는 손수건을 다시 보수하였다. 그러나 그처럼 위험한 일에 손수건 을 다시 사용할 수는 없었다."""

# 문장을 '.'을 기준으로 분할
sentences = long_text.split('.')

# CSV 파일에 작성
with open('sentences.csv', mode='w', newline='') as file:
    writer = csv.writer(file)
    writer.writerow(['Sentences'])
    for sentence in sentences:
        writer.writerow([sentence])
```
```
import re
text = """
안녕하세요 안녕하세요 안녕하세요
"""
text = re.sub('"','', text)
print(text)

str1 = 'apple'
str2 = 'backer'
len1 = len(str1)
len2 = len(str2)
table = []
for i in range(len1+1):
  row = []
  for j in range(len2+1):
    row.append(0)
  table.append(row)

for i in range(1, len1+1):
  table[i][0] = i
for j in range(1, len2+1):
  table[0][j] = j

for i in range(1, len1+1):
  for j in range(1, len2+1):
    if str1[i-1] == str2[j-1]:
      table[i][j] = table[i-1][j-1]
    else:
      table[i][j] = min(table[i][j-1], table[i-1][j], table[i-1][j-1])+1
print(table[len1][len2])
```
#### 사전을 만들어서 최소편집거리로 추천해주는 함수
```
# 단어 리스트 만들기
# 최소편집거리 알고리즘을 통해 리스트의 단어와 입력받은 단어의 편집거리를 구함
# 입력된 단어와 리스트의 단어들 사이의 편집거리가 작은 단어 출력

f = open("./englishwords.txt", 'r')
line = f.readlines()
f.close()

words_list = []
for each in line:
  words_list.append(each.replace("\n", ''))
print(words_list)

user_word = input()   # 단어 입력받기

minEditDistance_list = []   # 편집거리 따로 리스트를 만듦

for word in words_list:    # 단어 리스트를 반복문으로 돌면서 입력받은 단어와 리스트에 있는 단어 사이의 편집거리 계산
  len1 = len(user_word)
  len2 = len(word)

  # dp = [[0] * (len2 + 1) for j in range(len1 + 1)]

  table = []    # user_word와 word의 table 생성
  for i in range(len1 + 1):
    row = []
    for j in range(len2 + 1):
      row.append(0)
    table.append(row)
# user_word와 word/ 리스트 첫번째 줄에 1 ~ len* + 1 입력   
  for i in range(1, len1 + 1):
    table[i][0] = i
  for i in range(1, len2 + 1):
    table[0][i] = i
# 알고리즘
# [1, 1]부터 시작
  for i in range(1, len1 + 1):
    for j in range(1, len2 + 1):
      if user_word[i-1] == word[j-1]:
        table[i][j] = table[i-1][j-1]
      else:
        table[i][j] = min(table[i-1][j], table[i][j-1], table[i-1][j-1]) + 1

  minEditDistance_list.append(table[len1][len2])


# 단어 리스트와 편집거리 리스트를 딕셔너리로 만듦 (단어:편집거리) https://qna.programmers.co.kr/questions/8551/%ED%8C%8C%EC%9D%B4%EC%8D%AC-%EB%91%90%EA%B0%9C%EC%9D%98-%EB%A6%AC%EC%8A%A4%ED%8A%B8%EB%A5%BC-%EB%94%95%EC%85%94%EB%84%88%EB%A6%AC%EC%9D%98-%ED%82%A4key%EC%99%80-%EB%B0%B8%EB%A5%98value-%EA%B0%92%EC%9C%BC%EB%A1%9C-%EB%84%A3%EC%9D%84-%EC%88%98-%EC%9E%88%EB%82%98%EC%9A%94
dic = {name:value for name, value in zip(words_list, minEditDistance_list)}

minEditDistance_list.sort()   # 편집거리를 sort함

# minED_list = minEditDistance_list

minEditDistance_set = set(minEditDistance_list)

minED_list = list(minEditDistance_set)    # 리스트를 튜플 -> 리스트 변환(중복 제거)

min_word_list = []

# 최소편집거리가 제일 작은 두개의 인덱스를 이용해 딕셔너리 key값 출력
for i in range(0, 2): 
  for key,value in dic.items():
    if value == minED_list[i]:
      min_word_list.append(key)
print(min_word_list)
```
## Week 4: Vector Semantics and Embeddings

원형은 같아도 뜻은 다른 것 > 동의어
<p><strong>synonyms(동의어)</strong></p>
<p><strong>antonymy(반의어)</strong></p>
단어는 감정적 의미나 함축적 의미를 가지며 단어 내포는 분야에 따라 다른 의미를 갖는다.

## Week 4-2
### 벡터 의미론
<p>50년대의 첫 번째 아이디어는 언어 사용의 분포, 즉 인접한 단어나 문법적 환경을 의미하는 단어의 의미를 정의하는 것입니다.</p>

#### VAD vector 개념
<ol>
  <li>Valence(감정극성): 양의 감정(positive)과 부정의 감정(negative) 사이의 범위로 감정의 긍정적인 정도를 나타냅니다.</li>
  <li>Arousal(감정활성도): 개인의 감정적인 반응의 강도를 나타냅니다. 높은 arousing은 감정적으로 매우 활성화되어 있다는 것을 의미하며, 낮은 arousing은 감정적으로 안정되어 있다는 것을 나타냅니다.</li>
  <li>Dominance(감정지배력): 개인이 감정 상황을 지배하고 있는 정도를 나타냅니다. 높은 dominance는 감정이 개인에게 지배되는 것을 의미하며, 낮은 dominance는 개인이 감정에 지배되는 것을 나타냅니다.</li>
</ol>

#### embeddings
<ol>
  <li><strong><a href='https://ko.wikipedia.org/wiki/Tf-idf'>tf-idf</a></strong>: 단어의 출현 빈도를 기반으로 단어의 중요도를 계산하는 방법</li>
  <li><strong><a href='https://www.tensorflow.org/tutorials/text/word2vec?hl=ko'>Word2vec</a></strong>: 단어 간 관계를 고려하여 벡터를 계산하는 방법</li>
</ol>

### tf-idf 알고리즘
<p>문서의 특정 단어의 상대적 중요성을 평가하는 방법임<br>문서 내에서 자주 나타나지만 전반적으로 드물게 나타나는 단어는 높은 tf-idf값을 가짐</p>
<p>tf와 idf를 곱하여 계산함</p>
<p>단어 t의 tf-idf값 계산<br>tf-idf(t,d) = tf(t,d) * idf(t)<br><li>d는 문서를 나타냄</li><li>tf(t,d)는 단어 t의 문서 d에서의 빈도</li><li>idf(t)는 단어 t의 역문서 빈도</li>idf(t)값은 다음과 같이 계산됨<li>idf(t) = log(N / df(t))</li><li>N은 전체 문서의 수</li><li>df(t)는 단어 t가 나타나는 문서의 수</li><li>log 함수는 idf값을 스케일링 하는 역할</li></p>

### PMI(Pointwise Mutual Information)
<p>일반적으로 corpus에서 두 이벤트 간의 연관성을 측정하기 위해 사용됨<br>두 이벤트가 독립적인 경우보다 얼마나 함께 발생할 가능성이 있는지를 측정하는 지표</p>
PMI 공식
<p>PMI(X,Y) = log(P(X,Y)/(P(X)*P(Y)))<br>P(X,Y)는 X와Y가 함께 발생할 확률, P(X), P(Y)는 X와 Y가 각각 발생할 확률<br>PMI는 양수와 음수의 범위를 가지며, 양수면 두 이벤트 간의 연관성이 우연보다 강할 때, 음수면 반대의 의미를 가진다.</p>

### Word2Vec
<p></p>
"""
