## flask note
import os
from flask import Flask,request,session, \
g,redirect,url_for,abort,render_template,flash,request
import flask_whooshalchemy
from  flask_sqlalchemy import SQLAlchemy
from flask_wtf import FlaskForm
from wtforms import StringField,PasswordField,SelectField,SubmitField
from wtforms.validators import DataRequired,EqualTo

app=Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI']='mysql+pymysql://root:123456@127.0.0.1:3306/miniWeb'
app.config['SQLALCHEMY_TRACK_MODIFICATIONS']=False
app.secret_key='nihap'

db=SQLAlchemy(app)

#app.config['WHOOSH_BASE']='/home/yxr/svn/test/indexdir'
# app.config['DATABASE']='/'
# def connect_db():
# 	rv =sqlite3.connect(app.config['DATABASE'])
# 	rv.row_factory=sqlite3.Row
# 	return rv
# def init_db():
# 	with app.app_context():
# 		db=get_db()
# 		with app.open_source('schema.sql',mode='r') as f:
# 			db.cursor().execytescript(f.read())
# 		db.commit()
class Author(db.Model):
	__tablename__='authors'
	id=db.Column(db.Integer,primary_key=True)
	name=db.Column(db.String(16),unique=True)

	books=db.relationship('Book',backref='author')

class Book(db.Model):
	__tablename__='books'
	id=db.Column(db.Integer,primary_key=True)
	name=db.Column(db.String(16),unique=True)
	author_id=db.Column(db.Integer,db.ForeignKey('authors.id'))
class LoginForm(FlaskForm):
	author=StringField('author:',validators=[DataRequired()])
	book=StringField('book:',validators=[DataRequired()])
	submit=SubmitField('add book')


@app.route('/',methods=['get','post'])
def index():
	login_form=LoginForm()
	if request.method=='POST':
		
		if login_form.validate_on_submit():
			author=login_form.author.data
			book=login_form.book.data
			has_author = Author.query.filter_by(name=author).first()
			if has_author:
				has_book=Book.query.filter_by(name=book).first()
				if has_book:
					flash('the book is exist')
				else:
					try:
						new_book=Book(name=book,author_id=has_author.id)
						db.session.add(new_book)
						db.session.commit()

					except Exception as e:
						print(e)
						flash('add book failed')
						db.session.rollback()
			else:
				new_author=Author(name=author)
				db.session.add(new_author)
				db.session.commit()
				has_book=Book.query.filter_by(name=book).first()
				if has_book:
					flash('the book is exist')
				else:
					try:
						new_book=Book(name=book,author_id=new_author.id)
						db.session.add(new_book)
						db.session.commit()

					except Exception as e:
						print(e)
						flash('add book failed')
						db.session.rollback()

	authors=Author.query.all()
	return render_template('web.html',authors=authors,form=login_form)
@app.route('/delete_book/<book_id>')
def delete_book(book_id):
	has_book=Book.query.get(book_id)
	if has_book:
		try:
			db.session.delete(has_book)
			db.session.commit()
		except Exception as e:
			print(e)
			flash('delete book failed')
			db.session.rollback()
		return redirect(url_for('index'))
	else:
		flash('book not exist')

@app.route('/delete_author/<author_id>')
def delete_author(author_id):
	has_author=Author.query.get(author_id)
	if has_author:
		try:
			Book.query.filter_by(author_id=author_id).delete()

			db.session.delete(has_author)
			db.session.commit()
		except Exception as e:
			print(e)
			flash('delete author failed')
			db.session.rollback()
		return redirect(url_for('index'))
	else:
		flash('author not exist')
@app.route('/search/<query>')
def search(query):
	return 'return %s'%query



if __name__ == '__main__':
	db.drop_all()
	db.create_all()
	au1=Author(name='wang')
	book1=Book(name='1111',author_id=1)
	book2=Book(name='22',author_id=1)
	db.session.add_all([au1,book1,book2])
	db.session.commit()
	app.run(debug=True)
