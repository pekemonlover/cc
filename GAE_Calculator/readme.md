code for the project


main.py


import webapp2

class MainPage(webapp2.RequestHandler):

    def get(self):

        self.response.write("""
        <html>
        <head>
            <title>Calculator</title>
        </head>

        <body>

            <h2>Simple Calculator</h2>

            <form method="post">

                First Number:
                <input type="text" name="num1">

                <br><br>

                Second Number:
                <input type="text" name="num2">

                <br><br>

                Operation:

                <select name="operation">
                    <option value="add">Addition</option>
                    <option value="sub">Subtraction</option>
                    <option value="mul">Multiplication</option>
                    <option value="div">Division</option>
                </select>

                <br><br>

                <input type="submit" value="Calculate">

            </form>

        </body>
        </html>
        """)

    def post(self):

        num1 = float(self.request.get('num1'))
        num2 = float(self.request.get('num2'))
        operation = self.request.get('operation')

        result = ""

        if operation == "add":
            result = num1 + num2

        elif operation == "sub":
            result = num1 - num2

        elif operation == "mul":
            result = num1 * num2

        elif operation == "div":

            if num2 != 0:
                result = num1 / num2
            else:
                result = "Division by zero not allowed"

        self.response.write("""
        <html>
        <head>
            <title>Result</title>
        </head>

        <body>

            <h2>Calculation Result</h2>

            <h3>Result: %s</h3>

            <a href="/">Go Back</a>

        </body>
        </html>
        """ % result)



app.yaml

runtime: python27
api_version: 1
threadsafe: true

handlers:
- url: /.*
  script: main.app
app = webapp2.WSGIApplication([
    ('/', MainPage)
], debug=True)
