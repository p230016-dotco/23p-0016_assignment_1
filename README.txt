Knowledge Graph Extraction Project
This project reads text documents about famous physicists and a private document, and extracts factual relationships (called "triples") from them. The results are saved as a spreadsheet in out/triples.csv.
How to Run
1. Install the required tools: pip install spacy
2. Download the English language model: python -m spacy download en_core_web_sm
3. Run the script: python main.py
How the Rules Work
1. The Five Predicates
We extract five main types of relationships from the text:
* bornIn: We look for the word "born" and grab the location. We specifically ignore numbers so it doesn't accidentally grab a birth year.
* educatedAt: We look for words like "studied", "graduated", or "attended" and grab the name of the school or university.
* receivedAward: We look for words like "received", "won", or "earned" and grab the prize. We make sure to ignore degrees (like "bachelor's") and we clean up duplicates so we only get the longest version (e.g., we keep "Nobel Prize in Physics" and drop the shorter "Nobel Prize").
* worksAt: We look for words like "joined" or "worked at" and grab the name of the university or laboratory.
* doctoralAdvisor: We specifically look for sentences talking about an advisor and link the student to the advisor.
* (Bonus)bornOn / diedOn: We use a pattern to find birth and death dates (like "December 19, 1852 – May 9, 1931") and link them to the person.
2. Using the Grammar (Dependency Parse)
Instead of just looking at the order of words, the code looks at how words are grammatically connected to each other.
* For doctoralAdvisor, the code looks for the verb "was" and checks if the subject of the sentence contains the word "advisor". It then follows the grammatical link to the possessive name (the student) and the name after the verb (the advisor).
* For worksAt and educatedAt, it follows the grammatical "prepositional" links (like "at" or "from") to find exactly what place is attached to the action.
3. Pronouns ("He" and "She")
The code remembers the last male name and the last female name it saw. When it reads a sentence starting with "He" or "His", it replaces the pronoun with the last male name. If it sees "She" or "Her", it uses the last female name.
* Tricky case handled: In the private document, the sentence "He later moved to..." correctly refers to the male advisor, not the main female subject.
4. Negation
If the code sees words like "never", "not", or "n't" connected to the action word, it throws the whole fact away. This ensures that "She never studied at X" does not get recorded as a fact.
5. Same Entity, One Name
The text uses different names for the same person (like "Dr. Khokhar", "Khokhar", and "Rukhsana Khokhar"). We have a dictionary that maps all these nicknames and titles to one official full name. Every time the code makes this swap, it counts it and prints the total number of merges at the end of the script.
6. Provenance
Every single fact we write to the CSV includes the name of the document it came from (source_doc) and the exact original sentence it was found in (sentence), so you can always verify where the information came from.