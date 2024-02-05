# Get the training from the book
I just wanted to produce easily some stretching training from a (great) book I have and make a daily schedule in pdf.

## Getting all the exercises
I made a copy of the pdf where I only kept pages having the actual exercises. Then I extract the names of each exercise:

```python
import PyPDF2 #pip install pypdf2

import re

dflt_start_patterns = ['CALVES', 'THIGHS','HIPS','TRUNK','HANDS','CHEST', 'NECK', 'STRETCHES']
dflt_end_pattern = 'E7932'

def extract_relevant_text(input_string, start_patterns, end_pattern):
    # Initialize variables
    start_index = None
    
    # Check each start pattern to find the first match
    for start_pattern in start_patterns:
        start_match = re.search(start_pattern, input_string)
        if start_match:
            start_index = start_match.end()
            break  # Exit the loop if a match is found
    
    # Search for the end pattern
    end_match = re.search(end_pattern, input_string)
    
    # Extract the relevant part of the string if both start and end patterns are found
    if start_index is not None and end_match:
        end_index = end_match.start()
        relevant_text = input_string[start_index:end_index].strip()
        return ' '.join(relevant_text.split())
    else:
        return "Relevant text not found"


def get_first_line(pdf_file, start_patterns=dflt_start_patterns, end_pattern=dflt_end_pattern):
    all_titles = []
    with open(pdf_file, 'rb') as pdf:
        pdf_reader = PyPDF2.PdfReader(pdf)
        for page in pdf_reader.pages:
            input_string = page.extract_text()
            title = extract_relevant_text(input_string, start_patterns, end_pattern)
            all_titles.append(title)
    return all_titles
```

That gives me a basis for what I wanted. Adding to each exercise its category had to be done manually though. The result looks like this:

```python
dflt_all_tuples =[('BEGINNER SEATED TOE EXTENSOR STRETCH', 'FEET, CALVES'),
 ('ADVANCED STANDING TOE EXTENSOR STRETCH','FEET, CALVES'),
 ('BEGINNER SEATED TOE FLEXOR STRETCH','FEET, CALVES'),
 ...,
 ('INTERMEDIATE STANDING KNEE FLEXOR STRETCH','KNEES, THIGHS'),
 ('ADVANCED SEATED KNEE FLEXOR STRETCH','KNEES, THIGHS'),
 ...,
]
```

# Make a weekly schedule
Exercises are grouped by categories. All exercises in a given category are evenly split into 7 groups (or for each day of the week).

```python
import random
from collections import defaultdict

def exercise_to_page_number(exercise_name, all_titles = dflt_all_titles):
    pages_dict={exercise_name:i for i, exercise_name in enumerate(all_titles)}
    return pages_dict[exercise_name]

# Group exercises by category
def mk_category_exercises(dflt_all_tuples):
    category_exercises = defaultdict(list)
    for exercise, category in dflt_all_tuples:
        category_exercises[category].append(exercise)
    return category_exercises

# Function to divide the exercises into 7 groups
def divide_into_weekdays(exercises):
    random.shuffle(exercises)  # Shuffle the exercises
    n = len(exercises)
    # Calculate the size of each group
    quotient, remainder = divmod(n, 7)
    groups = [quotient + 1 if i < remainder else quotient for i in range(7)]
    
    # Distribute the exercises into 7 groups
    weekdays = []
    start = 0
    for size in groups:
        weekdays.append(exercises[start:start + size])
        start += size
    return weekdays

def flatten(l):
    return [item for sublist in l for item in sublist]

# Create a weekly schedule for each category
def mk_weekly_schedule(all_tuples=dflt_all_tuples):
    category_exercises = mk_category_exercises(all_tuples)
    weekly_schedule = {category: divide_into_weekdays(exercises) for category, exercises in category_exercises.items()}
    d={}
    for i in range(1,8):
        d[f'Day {i}']=[]
        for category, week in weekly_schedule.items():
            d[f'Day {i}'].append([exercise_to_page_number(exercise) for exercise in week[i-1]])
        
    
    return {k:flatten(v) for k,v in d.items()}
```

# Print the training for a given day

Now it is just a matter of gathering the relevant pages, for a given day, into a single pdf:
```python
def mk_pdf_from_weekly_schedule(weekly_schedule, pdf_file, output_filename, day_number=1):
    pages = weekly_schedule[f'Day {day_number}']
    pdf_writer = PyPDF2.PdfWriter()
    pdf_reader = PyPDF2.PdfReader(pdf_file)
    for page_num in pages:
            page = pdf_reader.pages[page_num]
            pdf_writer.add_page(page)

    with open(output_filename, 'wb') as output_filename:
        pdf_writer.write(output_filename)

# Example usage:
weekly_schedule = mk_weekly_schedule()
mk_pdf_from_weekly_schedule(weekly_schedule, pdf_file, output_filename='Monday_feb_5.pdf', day_number=1)
```