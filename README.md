# AiMath--
Шағын модельді AI.PYTHON тілін қолдайды және көп жүктеме артып жүрмейді

КОД:
```python
class MathModel:

    # 1) символдарды қалыпқа келтіру
    def normalize(self, text):
        text = text.replace('x', '*')
        text = text.replace('×', '*')
        text = text.replace(':', '/')
        text = text.replace('^','**')
        return text


    # 2) токенге бөлу
    def tokenize(self, text):
        tokens = []
        num = ""

        for ch in text:
            if ch.isdigit():
                num += ch
            else:
                if num:
                    tokens.append(int(num))
                    num = ""
                if ch != " ":
                    tokens.append(ch)

        if num:
            tokens.append(int(num))

        return tokens


    # 3) көбейту және бөлу
    def solve_mul_div(self, tokens, steps):
        i = 0
        while i < len(tokens):
            if tokens[i] == '*' or tokens[i] == '/':
                a = tokens[i-1]
                b = tokens[i+1]

                if tokens[i] == '*':
                    res = a * b
                    steps.append(f"Көбейтеміз: {a} * {b} = {res}")
                else:
                    res = a / b
                    steps.append(f"Бөлеміз: {a} : {b} = {res}")

                tokens[i-1:i+2] = [res]
                i = 0
            else:
                i += 1

        return tokens


    # 4) қосу және азайту
    def solve_add_sub(self, tokens, steps):
        i = 0
        while i < len(tokens):
            if tokens[i] == '+' or tokens[i] == '-':
                a = tokens[i-1]
                b = tokens[i+1]

                if tokens[i] == '+':
                    res = a + b
                    steps.append(f"Қосамыз: {a} + {b} = {res}")
                else:
                    res = a - b
                    steps.append(f"Азайтамыз: {a} - {b} = {res}")

                tokens[i-1:i+2] = [res]
                i = 0
            else:
                i += 1

        return tokens


    # 5) жақшаны шешу
    def solve_brackets(self, tokens, steps):

        while '(' in tokens:
            start = len(tokens) - 1 - tokens[::-1].index('(')
            end = tokens.index(')', start)

            inside = tokens[start+1:end]

            steps.append("Жақшаның ішін есептейміз")

            inside = self.solve_mul_div(inside, steps)
            inside = self.solve_add_sub(inside, steps)

            tokens[start:end+1] = inside

        return tokens


    # 6) негізгі функция
    def explain(self, text):

        steps = []
        text = self.normalize(text)

        steps.append(f"{text} есебін бірге шығарайық")

        tokens = self.tokenize(text)

        tokens = self.solve_brackets(tokens, steps)
        tokens = self.solve_mul_div(tokens, steps)
        tokens = self.solve_add_sub(tokens, steps)

        steps.append(f"Яғни,біздің жауабымыз {tokens[0]} болады \n")

        return "\n".join(steps)
        
def ex(task):
    m=MathModel()
    print(m.explain(task))
    
    
    #---------------------------#
    
    


#ex('((5*2-3+8)/3-2)*27')
```
