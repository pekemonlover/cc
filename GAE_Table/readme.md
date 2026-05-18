
main.py

import webapp2

class MainPage(webapp2.RequestHandler):

    def get(self):

        self.response.write("""
        <html>
        <head>
            <title>Table of 10</title>
        </head>

        <body>

            <h2>Table of 10</h2>
        """)

        for i in range(1, 11):

            result = 10 * i

            self.response.write(
                "10 x %d = %d <br>" % (i, result)
            )

        self.response.write("""
        </body>
        </html>
        """)

app = webapp2.WSGIApplication([
    ('/', MainPage)
], debug=True)


app.yaml
runtime: python27
api_version: 1
threadsafe: true

handlers:
- url: /.*
  script: main.app
