# uranai-system
初投稿です。ソウルナンバーを用いて、相性を判別してくれるシステムです。絶賛修正中です。

以下コード##2021/11/22
pip install transitions

!git clone https://github.com/kkuramitsu/tatoeba.git
from tatoeba.uranai import soul_number, ask_birthday

from transitions import Machine

#状態の定義
states = ['Q0','Q1','Q2','Q3']

#遷移の定義
transitions =[
    {'trigger':'E0','source':'Q0','dest':'Q1','before':'ask_Q0','after':'ask_Q1'},
    {'trigger':'E1','source':'Q1','dest':'Q2','after':'ask_Q2'},
    {'trigger':'E2','source':'Q2','dest':'Q3','after':'soul_number'},
    {'trigger':'E3','source':'Q3','dest':'Q0'}
]

#状態を管理したいオブジェクトの元となるクラス
class Uranai(object):
  def start(self):
    return self.ask_Q0()

  def ask_Q0(self):
    self.your_bday = ask_birthday('あなたの誕生日は？')

  def ask_Q1(self):
    self.name = input('好きな人の名前は？')
    return 'E1'

  def ask_Q2(self):
    self.like_bday = ask_birthday('好きな人の誕生日は？')
    return 'E2'

  def soul_number(self):
    y = soul_number(self.your_bday)
    a = soul_number(self.like_bday)
    AA = abs(y%11-a%11)
    #print('相性は(小さい程よい)',AA)
    if 2 <= AA < 5:
      print(self.name,'さんと相性はかなり良さそう！現状維持で生きましょう！')
      
    elif 5 <= AA < 10:
      print(self.name,'さんと相性はなかなか良さそうです！',self.name,'さんとあなたにラッキーなことが起こります！')
      
    elif 10 <= AA:
      print(self.name,'さんと急接近するかも...!?仲良くするチャンス！！')
      
    elif AA == 1 or AA == 0:
      print('おめでとう！',self.name,'さんと相性は凄く良さそう！相思相愛！')
      

  def ask_birthday(msg='生年月日は？'):
    date = input(msg)
    matched = re.findall(r'(\d\d\d\d)年|/|-(\d?\d)月|/|-(\d?\d)日?', date)
    if len(matched) > 0:
        year, month, day = matched[0]
        return year+month+day
    matched = re.findall(r'(\d?\d)月|/|-(\d?\d)日?', date)
    if len(matched) > 0:
        month, day = matched[0]
        return ask_birthyear()+month+day
    if not date.isdigit():
        raise RuntimeError('wrong format')
    return date

uranai = Uranai()
machine = Machine(model=uranai,states=states, transitions=transitions, initial = 'Q0' , auto_transitions=False)

def uranatte(self):
  uranai.trigger('E0')
  uranai.trigger('E1')
  uranai.trigger('E2')

STRING = str(input('「占いたい」と入力してね！占いが始まるよ！:'))
if STRING == '占いたい':
  uranatte(1)
else:
  print('入力エラーです！実行し直してください')
