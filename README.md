# csv1

package excel;
import java.io.BufferedWriter;
import java.io.FileReader;
import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Paths;
import java.util.ArrayList;
import java.util.List;
import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.Date;
import org.apache.commons.csv.CSVFormat;
import org.apache.commons.csv.CSVParser;
import org.apache.commons.csv.CSVPrinter;
import org.apache.commons.csv.CSVRecord;

public class CsvFileReader {
// change name of files as you're required or give proper path here.
    private static final String fileName="a.csv";
    private static final String newfile="fa.csv";
    
    
	public static class data{
		String Continent;
		String Country;
		String City;
		String Postcode;
		String Group;
		String Tenant;
		String MembershipStatus;
		String Logindate;
		String Payment;
		public data(String continent, String country, String city, String postcode, String group, String tenant,
				String membershipStatus, String logindate, String payment) {
			Continent = continent;
			Country = country;
			City = city;
			Postcode = postcode;
			Group = group;
			Tenant = tenant;
			MembershipStatus = membershipStatus;
			Logindate = logindate;
			Payment = payment;
		}
		
		
	}
	//CSV file header
    private static final String [] FILE_HEADER_MAPPING = {"Continent","Country","City","Postcode","Group","Tenant","Membership Status", "Login date", "Payment"};

    
    public static void main(String[] args) throws IOException, ParseException {
    	ArrayList<data> data=readCsvFile(fileName);
    	ArrayList<data> myrows=new ArrayList<data>();
    	ArrayList<Integer> indexes=new ArrayList<Integer>();
    	ArrayList<String> Groups=new ArrayList<String>();
    	
    	for(int i=0;i<data.size();i++) {
    		if (!Groups.contains(data.get(i).Group)) {
    			Groups.add(data.get(i).Group);
    		}

    	}
    	

    	for(int j=0;j<Groups.size();j++) {
    		String tPostCode="";

    		String ss = "9/9/1900";
        	Date cDate=new SimpleDateFormat("dd/MM/yyyy").parse(ss);
    		int index=0;
    		int index1=0;
    		int index2=0;
    		for(int i=0;i<data.size();i++) {
    			String gname=Groups.get(j);
    			if(gname.equals(data.get(i).Group)) {
    				String postCode=data.get(i).Postcode;
    				tPostCode=data.get(i).Postcode;
	    				for(int k=0;k<data.size();k++) {
    					
	    					if(postCode.equals(data.get(k).Postcode) && gname.equals(data.get(k).Group)) {
	    						String tdate=data.get(k).Logindate;
	    						Date date1=new SimpleDateFormat("dd/MM/yyyy").parse(tdate);
	    							if (cDate.compareTo(date1) < 0 ) {
	    								cDate=date1;
	    								index=k;
	    							}
	    						if(cDate.compareTo(date1)==0) {

	    							int a=Integer.parseInt(data.get(k).Payment);
	    							int b=Integer.parseInt(data.get(index).Payment);
	    							if(a>b) {
	    								index=k;
	    							}
	    							
	    						}
	    				        }
	    					
	    					
	    					if(!tPostCode.equals(data.get(k).Postcode) && gname.equals(data.get(k).Group)) {
	    						tPostCode=data.get(k).Postcode;
	    						index1=k;

	    					}    					
	    				}

	    				indexes.add(index);
	    				indexes.add(index1-2);
	    				break;
    			}
    		}
    	}

    	
        BufferedWriter writer = Files.newBufferedWriter(Paths.get(newfile));
        
        CSVPrinter csvPrinter = new CSVPrinter(writer, CSVFormat.DEFAULT
                .withHeader("Continent","Country","City","Postcode","Group","Tenant","Membership Status", "Login date", "Payment"));
    	int o=0;
    	for(int i=0;i<indexes.size();i++) {
    		int p=indexes.get(o);
                    csvPrinter.printRecord(data.get(p).Continent, data.get(p).Country, data.get(p).City, data.get(p).Postcode
                    		,data.get(p).Group,data.get(p).Tenant, data.get(p).MembershipStatus,data.get(p).Logindate,data.get(p).Payment);
               

    		System.out.println(data.get(p).Logindate);
    		
    		o++;
    	}
    	csvPrinter.flush();
    	System.out.println("new CSV created, check project folder please");
    }	
    
    
    
    
    
    
	public static ArrayList<data> readCsvFile(String fileName) {

		FileReader fileReader = null;
		
		CSVParser csvFileParser = null;
		
		//Create the CSVFormat object with the header mapping
        CSVFormat csvFileFormat = CSVFormat.DEFAULT.withHeader(FILE_HEADER_MAPPING);
     
        try {

        	ArrayList<data> rows = new ArrayList<data>();
            
            //initialize FileReader object
            fileReader = new FileReader(fileName);
            
            //initialize CSVParser object
            csvFileParser = new CSVParser(fileReader, csvFileFormat);
            
            //Get a list of CSV file records
            List csvRecords = csvFileParser.getRecords(); 
           for (int i = 1; i < csvRecords.size(); i++) {
            	CSVRecord record = (CSVRecord) csvRecords.get(i);
            	data row = new data(record.get("Continent"), record.get("Country"), record.get("City"), record.get("Postcode"),record.get("Group"),record.get("Tenant"),record.get("Membership Status"),record.get("Login date"), record.get("Payment"));
           	rows.add(row);	
			}
           return rows;
        } 
        catch (Exception e) {
        	System.out.println("Error in CsvFileReader !!!");
            e.printStackTrace();
        } finally {
            try {
                fileReader.close();
                csvFileParser.close();
            } catch (IOException e) {
            	System.out.println("Error while closing fileReader/csvFileParser !!!");
                e.printStackTrace();
            }
        }
		return null;

	}


}
