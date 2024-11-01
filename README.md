```python
import difflib
import json
import fitz  # PyMuPDF for handling PDF files
import subprocess
import pypandoc
from PyQt5.QtWidgets import QApplication, QWidget, QVBoxLayout, QHBoxLayout, QTextEdit, QPushButton, QLabel, QFileDialog
from docx import Document
from bs4 import BeautifulSoup
import pandas as pd
import sys

class DiffViewer(QWidget):
    def __init__(self):
        super().__init__()
        self.text1 = ""
        self.text2 = ""

        self.initUI()

    def initUI(self):
        # Main layout
        main_layout = QVBoxLayout()

        # Instructions label
        label = QLabel("Yashil matn qo‘shildi degani, qizil matn esa olib tashlandi degani va qora matn o‘zgarmadi degani.")
        main_layout.addWidget(label)

        # Horizontal layout for split text entry (left and right side)
        text_entry_layout = QHBoxLayout()
        
        # Left side (First text input)
        self.text_input1 = QTextEdit()
        self.text_input1.setPlaceholderText("Birinchi matnni shu yerga kiriting...")
        text_entry_layout.addWidget(self.text_input1)

        # Right side (Second text input)
        self.text_input2 = QTextEdit()
        self.text_input2.setPlaceholderText("Ikkinchi matnni shu yerga kiriting...")
        text_entry_layout.addWidget(self.text_input2)

        # Add the split text entry layout to main layout
        main_layout.addLayout(text_entry_layout)

        # Button layout
        button_layout = QHBoxLayout()

        # Button to load files
        btn_load_files = QPushButton("Faylni yuklash")
        btn_load_files.clicked.connect(self.load_files)
        button_layout.addWidget(btn_load_files)

        # Button to generate the diff
        btn_generate_diff = QPushButton("Farqni ko'rsatish")
        btn_generate_diff.clicked.connect(self.show_diff)
        button_layout.addWidget(btn_generate_diff)

        # Add buttons to the main layout
        main_layout.addLayout(button_layout)

        # TextEdit for displaying the diff
        self.diff_view = QTextEdit()
        self.diff_view.setReadOnly(True)
        main_layout.addWidget(self.diff_view)

        # Set up the main window
        self.setLayout(main_layout)
        self.setWindowTitle("Matnni farqlash oynasi")
        self.resize(800, 600)
    
    def load_files(self):
        # Load the first file
        file1, _ = QFileDialog.getOpenFileName(self, "Birinchi faylni tanlang")
        if file1:
            self.text1 = self.extract_text_from_file(file1)
            self.text_input1.setPlainText(self.text1)  # Display the content in the first text area

        # Load the second file
        file2, _ = QFileDialog.getOpenFileName(self, "Ikkinchi faylni tanlang")
        if file2:
            self.text2 = self.extract_text_from_file(file2)
            self.text_input2.setPlainText(self.text2)  # Display the content in the second text area

    def extract_text_from_file(self, filepath):
        # Handle different file types, including Markdown
        if filepath.endswith(".md"):
            # Convert Markdown to plain text for comparison
            return pypandoc.convert_file(filepath, 'plain', format='md')
        elif filepath.endswith(".pdf"):
            # PDF handling using PyMuPDF
            doc = fitz.open(filepath)
            text = ""
            for page in doc:
                text += page.get_text()
            return text
        elif filepath.endswith(".docx"):
            # DOCX handling using python-docx
            doc = Document(filepath)
            return "\n".join([p.text for p in doc.paragraphs])
        elif filepath.endswith(".html"):
            # HTML handling using BeautifulSoup
            with open(filepath, 'r', encoding='utf-8') as f:
                soup = BeautifulSoup(f, 'html.parser')
                return soup.get_text()
        else:
            # Plain text
            with open(filepath, 'r', encoding='utf-8') as f:
                return f.read()

    def show_diff(self):
        # Get texts from inputs
        text1 = self.text_input1.toPlainText()
        text2 = self.text_input2.toPlainText()

        # Generate diff
        diff = difflib.HtmlDiff().make_file(
            text1.splitlines(), text2.splitlines(), context=True, numlines=2
        )

        # Display diff in HTML format
        self.diff_view.setHtml(diff)

# Application initialization
if __name__ == "__main__":
    app = QApplication(sys.argv)
    viewer = DiffViewer()
    viewer.show()
    sys.exit(app.exec_())
```
