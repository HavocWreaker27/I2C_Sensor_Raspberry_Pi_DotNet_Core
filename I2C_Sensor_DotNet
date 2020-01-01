using System;
using System.Collections.Generic;
using System.Runtime.InteropServices;
using System.Threading;

namespace I2C.NetCore
{
    class Program
    {
        [DllImport("libc.so.6", EntryPoint = "open")]
        public static extern int Open(string fileName, int mode);

        [DllImport("libc.so.6", EntryPoint = "ioctl", SetLastError = true)]
        private extern static int Ioctl(int fd, int request, int data);

        [DllImport("libc.so.6", EntryPoint = "read", SetLastError = true)]
        internal static extern int Read(int handle, byte[] data, int length);

        [DllImport("libc.so.6", EntryPoint = "write", SetLastError = true)]
        internal static extern int Write(int handle, byte[] data, int length);

        private static int OPEN_READ_WRITE = 2; // Constant for all devices on I2C bus 1
        private static int I2C_SLAVE = 0x0703; // constant, even for different devices
        

        public static void i2c_camera_settings()
        {
            // read from I2C device bus 1 
            var i2cBushandle = Open("/dev/i2c-1", OPEN_READ_WRITE);

            // open the slave device at address 0x48 for communication
            int registerAddress = 0x58;
            var deviceReturnCode = Ioctl(i2cBushandle, I2C_SLAVE, registerAddress);

            byte[] write1 = { 0x30, 0x08 }; //2
            byte[] write2 = { 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x90, 0x00, 0x41 }; //10
            byte[] write3 = { 0x1A, 0x40, 0x00 }; //3
            byte[] write4 = { 0x1A, 0x40, 0x00 }; //3
            byte[] write5 = { 0x33, 0x33 }; //2
            byte[] write6 = { 0x30, 0x08 }; //2

            Write(i2cBushandle, write1, 2);
            //Sleep for 250 milliseconds or .25 seconds
            Thread.Sleep(250);

            Write(i2cBushandle, write2, 10);
            //Sleep for 250 milliseconds or .25 seconds
            Thread.Sleep(250);

            Write(i2cBushandle, write3, 3);
            //Sleep for 250 milliseconds or .25 seconds
            Thread.Sleep(250);

            Write(i2cBushandle, write4, 3);
            //Sleep for 250 milliseconds or .25 seconds
            Thread.Sleep(250);

            Write(i2cBushandle, write5, 2);
            //Sleep for 250 milliseconds or .25 seconds
            Thread.Sleep(250);

            Write(i2cBushandle, write6, 2);
            //Sleep for 250 milliseconds or .25 seconds
            Thread.Sleep(250);
            Console.WriteLine($"Write Bytes Complete");

        }

        static void Main(string[] args)
        {
            Console.WriteLine($"Program Started");
            int[] Ix = new int[4];
            int[] Iy = new int[4];
            int s;

            i2c_camera_settings();
            Thread.Sleep(5000);
            // read from I2C device bus 1 
            var i2cBushandle = Open("/dev/i2c-1", OPEN_READ_WRITE);

            // open the slave device at address 0x48 for communication
            int registerAddress = 0x58;
            var deviceReturnCode = Ioctl(i2cBushandle, I2C_SLAVE, registerAddress);
            var deviceDataInMemory = new byte[16];

            
            int inf;
            for (inf = 0; inf != 10;)
            {
                byte[] buf = { 0x36 }; //1
                Write(i2cBushandle, buf, 1);
                
                if (Read(i2cBushandle, deviceDataInMemory, 16) != 16)
                {
                    Console.WriteLine($"Unable to read from slave. Can't get 16 bytes");
                }
                else
                {
                    
                    Ix[0] = deviceDataInMemory[1];
                    Iy[0] = deviceDataInMemory[2];
                    s = deviceDataInMemory[3];
                    Ix[0] = deviceDataInMemory[1] | ((deviceDataInMemory[3] >> 4) & 0x03) << 8;
                    Iy[0] = deviceDataInMemory[2] | ((deviceDataInMemory[3] >> 6) & 0x03) << 8;

                    Ix[1] = deviceDataInMemory[4];
                    Iy[1] = deviceDataInMemory[5];
                    s = deviceDataInMemory[6];
                    Ix[1] += (s & 0x30) << 4;
                    Iy[1] += (s & 0xC0) << 2;

                    Ix[2] = deviceDataInMemory[7];
                    Iy[2] = deviceDataInMemory[8];
                    s = deviceDataInMemory[9];
                    Ix[2] += (s & 0x30) << 4;
                    Iy[2] += (s & 0xC0) << 2;

                    Ix[3] = deviceDataInMemory[10];
                    Iy[3] = deviceDataInMemory[11];
                    s = deviceDataInMemory[12];
                    Ix[3] += (s & 0x30) << 4;
                    Iy[3] += (s & 0xC0) << 2;

                    Console.WriteLine($"{Ix[0]},{Iy[0]},{Ix[1]},{Iy[1]},{Ix[2]},{Iy[2]},{Ix[3]},{Iy[3]}");
                }

            }
            
        } 
    }
}
