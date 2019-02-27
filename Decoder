package app;

import java.io.File;
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;

import ac.ArithmeticDecoder;
import io.InputStreamBitSource;
import io.InsufficientBitsLeftException;

public class DifferentialDecoder {
	
	public static void main(String[] args) throws InsufficientBitsLeftException, IOException {
		String input_file_name = "data/static-compressed.dat";
		String output_file_name = "data/reuncompressed.txt";

		FileInputStream fis = new FileInputStream(input_file_name);

		InputStreamBitSource bit_source = new InputStreamBitSource(fis);
		int numSymbols = (int) new File(input_file_name).length();
		System.out.println("compressed file length: " + numSymbols);
		// Read in symbol counts and set up model
		
		int[] symbol_counts = new int[511];
		Integer[] symbols = new Integer[511];
		
		for (int i=0; i<511; i++) {
			symbol_counts[i] = bit_source.next(32);
			symbols[i] = i - 255;
		}

		FreqCountIntegerSymbolModel model = 
				new FreqCountIntegerSymbolModel(symbols, symbol_counts);
		
		// Read in number of symbols encoded

		int num_symbols = bit_source.next(32);

		// Read in range bit width and setup the decoder

		int range_bit_width = bit_source.next(8);
		ArithmeticDecoder<Integer> decoder = new ArithmeticDecoder<Integer>(range_bit_width);

		// Decode and produce output.
		
		System.out.println("Uncompressing file: " + input_file_name);
		System.out.println("Output file: " + output_file_name);
		System.out.println("Range Register Bit Width: " + range_bit_width);
		System.out.println("Number of symbols: " + num_symbols);
		
		FileOutputStream fos = new FileOutputStream(output_file_name);
		
		int prevSym = 0;
		for (int i=0; i<num_symbols; i++) {
			//System.out.println(i);
			int sym = decoder.decode(model, bit_source);
			if (i == 0) {
				fos.write(sym);
				System.out.println(sym);
				prevSym = sym;
			} else {
				int encodingSym = prevSym - sym;
				fos.write(encodingSym);
				prevSym = encodingSym;
//				if (i < 5) {
//					System.out.println(encodingSym);
//				}
			}
			
		}

		System.out.println("Done.");
		fos.flush();
		fos.close();
		fis.close();
		
		FileInputStream fis2 = new FileInputStream(output_file_name);
		InputStreamBitSource bit_source2 = new InputStreamBitSource(fis2);
		int numSymbols2 = (int) new File(output_file_name).length();
		
		String original_file = "C:\\Users\\sokri\\Desktop\\Downloads\\out.dat";
		FileInputStream fis3 = new FileInputStream(original_file);
		InputStreamBitSource bit_source3 = new InputStreamBitSource(fis3);
		int numSymbols3 = (int) new File(original_file).length();
		
//		System.out.println("decoded length: " + numSymbols2 + " originial length " + numSymbols3);
//		boolean same = true;
//		for (int i = 0; i < numSymbols2; i ++) {
//			if (!(bit_source2.next(8) == bit_source3.next(8))) {
//				same = false;
//			}
//		}
//		System.out.println(same);
	}
	
}
