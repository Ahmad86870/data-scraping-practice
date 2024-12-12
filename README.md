# data-scraping-practice
import requests
from bs4 import BeautifulSoup
from urllib.parse import urlparse

def find_unique_external_links():
    url = "https://www.py4e.com/"
    response = requests.get(url)
    soup = BeautifulSoup(response.text, 'html.parser')
    
    links = soup.find_all('a', href=True)
    unique_links = set()

    for link in links:
        href = link['href']
        if href.startswith("https"):
            domain = urlparse(href).netloc
            unique_links.add(domain)
    
    print(f"Количество уникальных внешних ссылок: {len(unique_links)}")
    print("\nСписок внешних ссылок:")
    for link in sorted(unique_links):
        print(link)

if __name__ == "__main__":
    find_unique_external_links()

def count_comments():
    url = "https://py4e-data.dr-chuck.net/comments_42.html"
    response = requests.get(url)
    soup = BeautifulSoup(response.text, 'html.parser')
    
    comments = soup.find_all('span', class_='comments')
    total = sum(int(comment.text) for comment in comments)
    
    print(f"Общее количество комментариев: {total}")

# count_comments()

def find_unique_cities():
    url = "https://stackoverflow.com/jobs/companies"
    response = requests.get(url)
    soup = BeautifulSoup(response.text, 'html.parser')
    
    cities = set()
    locations = soup.find_all('div', class_='fc-black-500')
    
    for location in locations:
        city = location.text.strip()
        if city:
            cities.add(city)
    
    sorted_cities = sorted(cities)
    print("Города (в алфавитном порядке):")
    for city in sorted_cities:
        print(city)

# find_unique_cities()

def search_stackoverflow_questions(keyword, pages=5):
    base_url = "https://stackoverflow.com/questions"
    headers = {"User-Agent": "Mozilla/5.0"}
    results = []

    for page in range(1, pages + 1):
        url = f"{base_url}?tab=newest&page={page}"
        response = requests.get(url, headers=headers)
        soup = BeautifulSoup(response.text, 'html.parser')
        
        questions = soup.find_all('a', class_='question-hyperlink')
        for question in questions:
            title = question.text.lower()
            link = "https://stackoverflow.com" + question['href']
            if keyword.lower() in title:
                results.append((title, link))
    
    print(f"Результаты поиска по слову '{keyword}':")
    for idx, (title, link) in enumerate(results, 1):
        print(f"{idx}. {title}\n{link}\n")

# search_stackoverflow_questions("Python", pages=5)

if __name__ == "__main__":
    print("=== Задача 1: Поиск уникальных внешних ссылок ===")
    find_unique_external_links()
    
    print("\n=== Задача 2: Подсчёт комментариев ===")
    count_comments()
    
    print("\n=== Задача 3: Города из вакансий ===")
    find_unique_cities()
    
    print("\n=== Задача 4: Поиск вопросов на StackOverflow ===")
    keyword = input("Введите ключевое слово для поиска: ")
    search_stackoverflow_questions(keyword)
