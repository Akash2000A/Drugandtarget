import requests

def smiles_to_drug_name(smiles):
    try:
        # PubChem API endpoint for converting SMILES to CID (Compound ID)
        cid_url = "https://pubchem.ncbi.nlm.nih.gov/rest/pug/compound/smiles/{}/cids/TXT".format(smiles)
        response = requests.get(cid_url)
        if response.status_code != 200:
            return f"Failed to get CID for SMILES: {smiles}. HTTP Status Code: {response.status_code}"
        
        # Get the CID from the response
        
  cids = response.text.strip()
    if not cids:
    return f"No CID found for SMILES: {smiles}"
        
   cid = cids.split('\n')[0]
        
        # Use the CID to get the compound information
  name_url = f"https://pubchem.ncbi.nlm.nih.gov/rest/pug/compound/cid/{cid}/property/PreferredName/TXT"
  response = requests.get(name_url)
        
  if response.status_code != 200:
  return f"Failed to get drug name for CID: {cid}. HTTP Status Code: {response.status_code}"
        
        # Get the drug name from the response
  drug_name = response.text.strip()
        return drug_name
    
  except Exception as e:
        return f"Error processing SMILES {smiles}: {str(e)}"

def process_smiles_file(file_path):
    results = []
    with open(file_path, 'r') as file:
        for line in file:
            smiles = line.strip()
            drug_name = smiles_to_drug_name(smiles)
            results.append(f"{smiles}: {drug_name}")
    return results

def save_results_to_file(results, output_file_path):
    with open(output_file_path, 'w') as file:
        for result in results:
            file.write(result + '\n')

if __name__ == "__main__":
    input_file_path = '/home/akaz/Desktop/drug.txt'  # Replace with the actual path to your input file
    output_file_path = '/home/akaz/Desktop/drug_names.txt'  # Replace with the desired path for the output file

    # Process the SMILES file and get the results
    results = process_smiles_file(input_file_path)
    
    # Save results to a text file
    save_results_to_file(results, output_file_path)
    
    print(f"Results saved to {output_file_path}")# Drugandtarget
