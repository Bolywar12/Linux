import difflib
import json
import fitz  # PyMuPDF
import subprocess
import pypandoc
from PyQt5.QtWidgets import QApplication, QWidget, QVBoxLayout, QHBoxLayout, QTextEdit, QPushButton, QLabel, QFileDialog
from docx import Document
from bs4 import BeautifulSoup
import pandas as pd
import sys

class DiffViewer(QWidget):
    def init(self):
        super().init()
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