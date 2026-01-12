import os
from pathlib import Path
import shutil
from jinja2 import Template

def parse_info(info_path):
    """Parse info.txt file into a dictionary."""
    info = {}
    with open(info_path, 'r') as f:
        for line in f:
            if ':' in line:
                key, value = line.split(':', 1)
                info[key.strip()] = value.strip()
    return info

def get_people(category):
    """Get all people in a category (instructors or tas)."""
    people = []
    people_dir = Path(f'people/{category}')
    
    if not people_dir.exists():
        return people
    
    for person_dir in people_dir.iterdir():
        if person_dir.is_dir():
            info_file = person_dir / 'info.txt'
            photo_files = list(person_dir.glob('photo.*'))
            
            if info_file.exists() and photo_files:
                info = parse_info(info_file)
                photo = photo_files[0]
                
                # Copy photo to docs/images
                photo_dest = Path('docs/images') / f'{category}_{person_dir.name}{photo.suffix}'
                photo_dest.parent.mkdir(parents=True, exist_ok=True)
                shutil.copy(photo, photo_dest)
                
                people.append({
                    **info,
                    'photo': f'images/{photo_dest.name}',
                    'id': person_dir.name
                })
    
    return sorted(people, key=lambda x: x.get('name', ''))

def build_site():
    """Build the static site."""
    # Read template
    with open('template.html', 'r') as f:
        template = Template(f.read())
    
    # Get people data
    instructors = get_people('instructors')
    tas = get_people('tas')
    
    # Render HTML
    html = template.render(instructors=instructors, tas=tas)
    
    # Write to docs/index.html
    output_dir = Path('docs')
    output_dir.mkdir(exist_ok=True)
    
    with open(output_dir / 'index.html', 'w') as f:
        f.write(html)
    
    print(f"✓ Built site with {len(instructors)} instructors and {len(tas)} TAs")

if __name__ == '__main__':
    build_site()
