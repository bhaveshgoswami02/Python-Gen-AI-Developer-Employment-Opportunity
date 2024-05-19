README
Citations Extraction Tool
This Python program fetches data from a paginated API, identifies citations for each response, and displays the results in a user-friendly interface. The tool is designed to help you understand which sources contributed to the responses retrieved from the API.

Features
Fetch data from the paginated API endpoint.
Identify citations for each response based on the provided sources.
Present the results in a user-friendly interface using Jupyter widgets.
Display detailed information for selected responses and their corresponding citations.
Requirements
Python 3.x
requests library
pandas library
ipywidgets library
Jupyter Notebook or JupyterLab
Installation
Install the required Python libraries using pip:

bash
Copy code
pip install requests pandas ipywidgets
Ensure you have Jupyter Notebook or JupyterLab installed. If not, install it using pip:

bash
Copy code
pip install notebook
Usage
Open Jupyter Notebook or JupyterLab.

bash
Copy code
jupyter notebook
Create a new notebook and copy the provided code into a cell.

Run the cell to execute the program.

Code Explanation
1. Fetching Data from the API
The function get_data retrieves data from the paginated API endpoint. The API URL is set to fetch the first page of data.

python
Copy code
import requests
import pandas as pd
import ipywidgets as widgets
from IPython.display import display

def get_data():
    api_url = "https://devapi.beyondchats.com/api/get_message_with_sources?page=1"  # Replace with the actual API URL
    response = requests.get(api_url)
    if response.status_code == 200:
        data = response.json()
        return data
    else:
        print(f"Error: API call failed with status code {response.status_code}")
2. Identifying Citations
The function identify_citations processes the fetched data to identify citations for each response based on the provided sources.

python
Copy code
def identify_citations(data):
    results = []
    for item in data:
        response_text = item.get("response")
        sources = item.get("source", [])
        citations = []
        
        for source in sources:
            context = source.get("context", "")
            if response_text in context:
                del source["context"]
                citations.append(source)
        
        results.append({"response": response_text, "citations": citations})
    return results
3. Creating DataFrame for Citations
The function create_citations_dataframe converts the citations data into a pandas DataFrame for better visualization.

python
Copy code
def create_citations_dataframe(citations_data):
    data_for_df = []
    for item in citations_data:
        response_text = item["response"]
        for citation in item["citations"]:
            data_for_df.append({
                "response": response_text,
                "source_id": citation.get("id"),
                "source_link": citation.get("link", "")
            })
    return pd.DataFrame(data_for_df)
4. Displaying Data with Jupyter Widgets
The code below creates a user-friendly interface using Jupyter widgets to display the responses and their corresponding citations.

python
Copy code
# Convert citations data to DataFrame for better visualization in Jupyter
citations_data = identify_citations(get_data()['data']['data'])
citations_df = create_citations_dataframe(citations_data)

# Function to display data for a selected response
def display_response_data(response_index):
    response_data = citations_data[response_index]
    response_text = response_data["response"]
    citations = response_data["citations"]

    response_text_widget.value = response_text
    if citations:
        citations_df = pd.DataFrame(citations)
        citations_df_widget.value = citations_df.to_string()
    else:
        citations_df_widget.value = "No citations found."

# Create widgets
response_index_slider = widgets.IntSlider(min=0, max=len(citations_data)-1, step=1, description="Response Index")
response_text_widget = widgets.Textarea(value="", description="Response Text", layout=widgets.Layout(width="100%", height="100px"))
citations_df_widget = widgets.Textarea(value="", description="Citations", layout=widgets.Layout(width="100%", height="200px"))

# Display widgets
display(response_index_slider, response_text_widget, citations_df_widget)

# Update display based on slider value
widgets.interactive_output(display_response_data, {"response_index": response_index_slider})
Conclusion
This program provides an efficient way to fetch data from an API, identify sources for each response, and display the results in a user-friendly interface using Jupyter widgets. By following the instructions in this README, you can set up and run the program to analyze the citations for the responses retrieved from the API.