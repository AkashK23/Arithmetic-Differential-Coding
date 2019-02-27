package app;

import java.io.File;
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;

import ac.ArithmeticEncoder;
import io.OutputStreamBitSink;

public class DifferentialEncoding {
	public static void main(String[] args) throws IOException {
		String input_file_name = "C:\\Users\\sokri\\Desktop\\Downloads\\out.dat";
		String output_file_name = "data/static-compressed.dat";

		int range_bit_width = 40;

		System.out.println("Encoding text file: " + input_file_name);
		System.out.println("Output file: " + output_file_name);
		System.out.println("Range Register Bit Width: " + range_bit_width);

		int num_symbols = (int) new File(input_file_name).length();
		
		// Analyze file for frequency counts
		
		FileInputStream fis = new FileInputStream(input_file_name);

		int[] symbol_counts = new int[511];

		int next_byte = fis.read();
		//symbol_counts[next_byte + 255]++;
		
		int encoding_byte = next_byte;
		int old_byte = next_byte;
		int counter = 0;
		while (next_byte != -1) {
//			if (counter < 5) {
//				System.out.println(next_byte);
//			}
			symbol_counts[encoding_byte + 255]++;
			next_byte = fis.read();
			encoding_byte = old_byte - next_byte;
			old_byte = next_byte;
			counter++;
		}
		fis.close();
		
		Integer[] symbols = new Integer[511];
		for (int i=-255; i<256; i++) {
			symbols[i+255] = i;
		}

		// Create new model with analyzed frequency counts
		FreqCountIntegerSymbolModel model = new FreqCountIntegerSymbolModel(symbols, symbol_counts);

		ArithmeticEncoder<Integer> encoder = new ArithmeticEncoder<Integer>(range_bit_width);

		FileOutputStream fos = new FileOutputStream(output_file_name);
		OutputStreamBitSink bit_sink = new OutputStreamBitSink(fos);

		// First 256 * 4 bytes are the frequency counts 
		for (int i=0; i<511; i++) {
			bit_sink.write(symbol_counts[i], 32);
		}

		// Next 4 bytes are the number of symbols encoded
		bit_sink.write(num_symbols, 32);		

		// Next byte is the width of the range registers
		bit_sink.write(range_bit_width, 8);

		// Now encode the input
		fis = new FileInputStream(input_file_name);
		int prevSym = 0;
		for (int i=0; i<num_symbols; i++) {
			int next_symbol = fis.read();
			int enocdingSym;
			if (i == 0) {
				enocdingSym = next_symbol;
			} else {
				enocdingSym = prevSym - next_symbol;
			}
//			if (i < 5) {
//				System.out.println(enocdingSym);
//			}
			encoder.encode(enocdingSym, model, bit_sink);
			prevSym = next_symbol;
		}
		fis.close();

		// Finish off by emitting the middle pattern 
		// and padding to the next word
		
		encoder.emitMiddle(bit_sink);
		bit_sink.padToWord();
		fos.close();
		
		System.out.println("Done");
	}
}
