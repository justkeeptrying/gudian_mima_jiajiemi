# gudian_mima_jiajiemi
#Version2
#!/usr/bin/env python
# -*- coding: utf-8 -*-
# -*- coding: gbk -*-
#作者：培敦
#E-mail：13118628923@163.com

import string
import sys
from easygui import *
import random

s ='abcdefghijklmnopqrstuvwxyz'
lengths=len(s)
C = [1, 3, 5, 7, 9, 11, 15, 17, 19, 21, 23, 25] #单表乘法密钥空间

################################################加法密码######################################
def caesar(m,shift):
    news=''
    for x in m:        
        numx=s.index(x)             #这是一个注释
        numx=(numx+shift)%lengths   #把明文的每个字母移动密钥shift给的位数
        news+=s[numx]
    return news                     #得到新的字符串
    
################################################乘法密码######################################    
def chengfa(m, key):
    m = m.strip()
    news = ''
    for x in m:
        if x == ' ':                            #目前仅仅去除输入中的空格，还没有对其他的输入进行检查，限制
            pass                                #因为所有用户的输入都是不安全的
        else:
            #print x, 
            numx = s.index(x)
            numx = (numx*key) % lengths
            news += s[numx]
    return news

def extendedGCD(a, b):      #扩展欧几里得算法求逆元
    #a*xi + b*yi = ri
    if b == 0:
        #print '无逆元'
        return False
    #a*x1 + b*y1 = a
    x1 = 1
    y1 = 0
    #a*x2 + b*y2 = b
    x2 = 0
    y2 = 1
    while b != 0:
        q = a / b
        #ri = r(i-2) % r(i-1)
        r = a % b
        a = b
        b = r
        #xi = x(i-2) - q*x(i-1)
        x = x1 - q*x2
        x1 = x2
        x2 = x
        #yi = y(i-2) - q*y(i-1)
        y = y1 - q*y2
        y1 = y2
        y2 = y
    return x1

def jiechengfa(c, ukey):        #解密过程
    c = c.strip()
    news = ''
    for x in c:
        if x == ' ':
            pass
        else:
            numx = s.index(x)
            numx = (numx * ukey) % lengths
            news += s[numx]
    return news
        
################################################仿射密码######################################        
def fangshe(m, key1, key2):
    m = m.strip()
    news = ''
    for x in m:
        if x == ' ':
            pass
        else:
            numx = s.index(x)
            numx = (key1 + (numx * key2)) % lengths
            news += s[numx]
    return news

def jiefangshe(c, dkey1, dkey2):
    c = c.strip()
    news = ''
    for x in c:
        if x == ' ':
            pass
        else:
            numx = s.index(x)
            numx = ((numx - dkey1) * dkey2) % lengths
            news += s[numx]
    return news

################################################置换密码######################################    
class ECaesarCipher():
    def __init__(self, message=None):
        self.message = message
        self.cipherMessage = ''

        #获取‘a’到‘z’的顺序字符表
        pT = tuple(string.ascii_lowercase)

        #建立随机化的密文字符表
        cT=[]
        temp=range(0,26)
        random.shuffle(temp)
        for x in temp:
            cT.append(pT[x])

        #建立密码本，即明文，密文对照表，这里使用了字典结构
        self.keyTable={}
        for x,y in zip(pT,cT):
            self.keyTable[x]=y

    def encrypt(self):
        self.cipherMessage=""

        for x in self.message:
            if x == ' ':
                self.cipherMessage += ' '
            elif x.isupper():
                self.cipherMessage += self.keyTable[x.lower()].upper()
            else:
                self.cipherMessage += self.keyTable[x]
        return self.cipherMessage

    def decrypt(self):
        keyTableDec = dict((value,key) for key,value in self.keyTable.items())
        
        
        message = ''
        if self.cipherMessage:
            for x in self.cipherMessage:
                if x==' ':
                    message += ' '
                elif x.isupper():
                    message += keyTableDec[x.lower()].upper()
                else:
                    message += keyTableDec[x]
        return message

'''
算法建立了ECaesarCipher类，支持大小写英文字母、空格作文明文字符，
初始化时随机产生明密文对照表。这个明密文对照表也可以称为密码本。
'''
#讲真，写的真心累！！

################################################希尔密码###################################### 
alpha=['a','b','c','d','e','f','g','h','i','j','k','l','m','n','o','p','q','r','s','t','u','v','w','x','y','z']  
xalpha=['A','B','C','D','E','F','G','H','I','J','K','L','M','N','O','P','Q','R','S','T','U','V','W','X','Y','Z']  
  
#初始化多维数据  
def genMatrix(rows,cols):  
    matrix = [[0 for col in range(cols)] for row in range(rows)]  
    return matrix  
  
#为加密矩阵赋值  
def init_enc_Matrix(matrix):  
    matrix[0][0]=11  
    matrix[0][1]=8  
    matrix[1][0]=3  
    matrix[1][1]=7  
  
#为解密矩阵赋值  
def init_dec_Matrix(matrix):  
    matrix[0][0]=7  
    matrix[0][1]=18  
    matrix[1][0]=23  
    matrix[1][1]=11  
  
#打印矩阵  
def printMatrix(matrix,rows,cols):  
    for i in range(rows):  
        for j in range(cols):  
            print matrix[i][j],  
        print '\n'  
  
#二维矩阵相乘  
def MatrixTime(A,B):  
    x=(A[0]*B[0][0]+A[1]*B[1][0])%26  
    y=(A[0]*B[0][1]+A[1]*B[1][1])%26  
    C=[x,y]  
    return C  
  
#将字母转化为相对应的数字  
def Char2Num(c):  
    for alpha_num in range(len(alpha)):  
        if alpha[alpha_num] == c:  
            return alpha_num  
  
def Num2Char(n):  
    return alpha[n]  
  
#将用户输入的明文字符串赋值给多行两列的数组  
def initPlain(string):  
    rows = len(string)/2  
    cols = 2  
    plain_matrix=genMatrix(rows,cols)#矩阵plain_matrix为将明文字符串转换为的多行两列矩阵  
    plain_list=list(string)#将明文字符串转化为列表  
      
      
    #明文字符串转化为列表后，每个元素字母对应的数字值  
    plain_list_num=[0 for i in range (len(string))]  
    for k in range(len(plain_list_num)):  
        plain_list_num[k] = Char2Num(plain_list[k])  
      
    #将“数字”字符串转换为矩阵  
    index = 0  
    for i in range(rows):  
        for j in range(cols):  
            if index < len(string):  
                plain_matrix[i][j] = plain_list_num[index]  
                index = index+1  
    return plain_matrix  
  
#加密过程  
def hillencrypt(plain_matrix,k):  
    rows = len(plain_matrix)  
    cipher_matrix = genMatrix(rows,2)#初始化一个与plain_matrix结构一样的矩阵用来存储密文矩阵  
    for i in range(rows):  
        cipher_matrix[i] = MatrixTime(plain_matrix[i],k)  
    #到这里已经得到了加密后的矩阵，现在转换为普通字符串  
    cipher_list_num=[0 for i in range (rows*2)]  
    index = 0  
    for i in range(rows):  
        for j in range(2):  
            if index < rows*2:  
                cipher_list_num[index] = cipher_matrix[i][j]  
                index = index+1  
    ###################################################  
    ##return cipher_matrix  
    #print cipher_list_num  
    cipher_list_char=[0 for i in range (rows*2)]  
    for i in range(len(cipher_list_char)):  
        cipher_list_char[i] = Num2Char(cipher_list_num[i])  
    cipherstring = ''.join(cipher_list_char)  
    return cipherstring 
          

################################################维吉尼亚方阵######################################
#把加/解密密钥由字母转换成数字列表

#把加/解密密钥由字母转换成数字列表
def KEY(Key):                  #处理输入的密钥，把输入的密钥每个字母转化为相对应字母表的数字列表
    #key = raw_input('输入密钥: ')
    key = list(Key)
    raw_key_num = [0 for i in range (len(Key))]
    k = 0
    for item in Key:
        for j in range(len(alpha)):
            if item == alpha[j]:
                raw_key_num[k] = j
                k += 1
    return raw_key_num


def encrypt(rawstring):  
    'ENCRYPT FUNCTION'  
    rawlist=list(rawstring)#将原字符串分解为字母的列表，变量记为rawlist  
    rawlistnum=[0 for i in range (len(rawstring))]#原字符串对应数字的列表，变量记为rawlistnum  
    rawlen=len(rawlist)  
    k=0  
    for item in rawlist:  
        for j in range(len(alpha)):  
            if item == alpha[j]:  
                rawlistnum[k] = j  
                k=k+1  
    resultnum=[0 for i in range (len(rawstring))]#加密后各字母所对应的数字的列表，变量记为resultnum  
    for i in range(len(rawlistnum)):  
        resultnum[i]=(rawlistnum[i]+key[i%keylen])%26  
    resultchar=[0 for i in range (len(rawstring))]#加密后的各字母的列表，变量记为resultchar  
    a=0  
    for index in resultnum:  
        resultchar[a]=xalpha[index]  
        a=a+1  
    result = ''.join(resultchar)  
    return result  
  
def decrypt(rawstring):  
    'DECRYPT FUNCTION'  
    rawlist=list(rawstring)  
    rawlistnum=[0 for i in range (len(rawstring))]  
    rawlen=len(rawlist)  
    k=0  
    for item in rawlist:  
        for j in range(len(xalpha)):  
            if item == xalpha[j]:  
                rawlistnum[k] = j  
                k=k+1  
    resultnum=[0 for i in range (len(rawstring))]  
    for i in range(len(rawlistnum)):  
        resultnum[i]=(rawlistnum[i]-key[i%keylen])%26#解密函数，此处为减法  
    resultchar=[0 for i in range (len(rawstring))]  
    a=0  
    for index in resultnum:  
        resultchar[a]=alpha[index]#注意为alpha，非xalpha  
        a=a+1  
    result = ''.join(resultchar)  
    return result  
   
################################################结合界面######################################    
msgbox('欢迎进入古典密码加解密程序','古典密码',image = 'dalao.gif')

while 1:
    choice = ('凯撒密码','乘法密码','仿射密码','单表置换','希尔矩阵', '维吉尼亚方阵', '退出')
    answer = buttonbox(msg='请选择一种加解密方式 ', title=' 古典密码加解密 ', choices=choice)        
    if answer == '退出':
        sys.exit(0)
    try:
        if answer == '凯撒密码':
            m = enterbox('输入待加密明文: ', title='凯撒密码: ')
            shift = enterbox('移动位数： ')
            shift = int(shift)
            c = caesar(m, shift)
            d_caesar = caesar(c,int(shift)*(-1))
            msgbox('加密结果为：'+ c +'\n'+ '解密结果为：'+ d_caesar, title = '凯撒密码')
            
        elif answer == '乘法密码':
            m = enterbox('输入待加密明文: ', title = '乘法密码')
            key = enterbox('从（1, 3, 5, 7, 9, 11, 15, 17, 19, 21, 23, 25)中选择一个加密密钥: ')
            key = int(key)
            if key in C:
                c = chengfa(m, key)
                d_key = extendedGCD(key, lengths)
                M = jiechengfa(c, d_key)
                msgbox('加密结果为：' + c + '\n' + '逆元为：'+str(extendedGCD(key, lengths))+
                       '\n'+'解密结果为：'+ M, title = '乘法密码')     
            else:
                msgbox('密钥输入错误，请重新输入', title = 'ERROR!!', image = 'shishi.gif')
        
        elif answer == '仿射密码':
            m = enterbox('输入待加密明文：', title = '仿射密码')
            key1 = enterbox('输入第一个密钥K1：', title = '仿射密码')
            key2 = enterbox('输入第二个密钥K2：', title = '仿射密码')
            key1 = int(key1)
            key2 = int(key2)
            c = fangshe(m, key1, key2)
            M = jiefangshe(c, 3, 9)
            msgbox('加密结果为：' + c + '\n' + '解密结果为：'+ M +'\n', title = '仿射密码')
        
        elif answer == '单表置换':
            m = enterbox('输入待加密的明文：')
            ec = ECaesarCipher(m)
            ec_keytable = ec.keyTable   #随机密码本
            ec_encrypt = ec.encrypt()   #加密结果
            ec_decrypt = ec.decrypt()   #解密结果
            msgbox('随机得到的密码本为：'+ str(ec_keytable) +'\n'+ '加密结果为：' + ec_encrypt + '\n' +
                   '解密结果为：'+ ec_decrypt +'\n', title = '仿射密码')
        
        elif answer == '希尔矩阵':
            button = buttonbox(msg = '请选择加密或解密：(E)解密,(D)解密', title = '希尔矩阵', choices = ('E','D'))
            k=genMatrix(2,2)#初始化加密矩阵  
            reverse_k=genMatrix(2,2)#初始化解密矩阵  
            init_enc_Matrix(k)#为加密矩阵赋值  
            init_dec_Matrix(reverse_k)#为解密矩阵赋值  
             
            #cm = printMatrix(k,2,2)#打印加密矩阵      
            #dm = printMatrix(reverse_k,2,2)#打印解密矩阵  
        ##################################################################   
        #########################加密过程##################################    
            if button == 'E':
                plain= enterbox('请输入待加密明文（须为偶数）', title= '希尔矩阵')
                plain_matrix = initPlain(plain)
        
                cipher = hillencrypt(plain_matrix,k)  
                msgbox('加密结果为：'+ cipher)
                
            elif button == 'D':
                cipher = enterbox('请输入待解密明文', title= '希尔矩阵')
                cipher_matrix = initPlain(cipher)
        
                
                plain = hillencrypt(cipher_matrix,reverse_k)          
                msgbox('解密结果为：'+ plain)  
            else:  
                sys.exit(0)
                
        elif answer ==  '维吉尼亚方阵':
            
            button = buttonbox(msg = '请选择加密或解密：(E)解密,(D)解密', title = '维吉尼亚方阵', choices = ('E','D'))
            
            if button == 'E':  
                c = enterbox('请输入待加密明文 ', title= '维吉尼亚方阵')
                kk = enterbox(msg=' 请输入加密密钥：', title = '维吉尼亚方阵')
                key = KEY(kk)
                keylen = len(key)#取密钥长度是因为要考虑原文的长度是否比密钥长度大
                
                c = list(c.strip())
                #print plainstring
                newx = []
                for x in c:
                    if x == ' ':
                        pass 
                    else:
                        #print x
                        newx.append(x)
                c = ''.join(newx)
                cipherresult = encrypt(c)
                msgbox('加密结果为：'+ cipherresult+ '\n')
                  
            elif button == 'D':  
                d = enterbox('请输入待解密明文 ', title= '维吉尼亚方阵')
                kk = enterbox(msg=' 请输入解密密钥：', title = '维吉尼亚方阵')
                key = KEY(kk)
                keylen = len(key)#取密钥长度是因为要考虑原文的长度是否比密钥长度大
                
                d = list(d.strip())
                c_newx = []
                for y in d:
                    if y == ' ':
                        pass
                    else:
                        c_newx.append(y)
                d = ''.join(c_newx)
                plainresult = decrypt(d)  
                msgbox('解密结果为：'+ plainresult + '\n')  
            else:  
                sys.exit(0)        

    except:
        msgbox('程序出错, 请重新输入','ERROR!!',image = 'shishi.gif')
        if ccbox('是否继续？',choices = ('再玩一次','丑拒')):
            pass 
        else:
            sys.exit(0)    
